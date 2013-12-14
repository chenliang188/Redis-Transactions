### The problem ###

The particular problem is that Redis stores precomputed, semi-finished data to facilitate queries.  You can visualize that the data is sort of "denormalized".  For the time series, **the same** data point may be computed at different aggregation levels (minute, hour, day) for different statistics (avg, max, n).  The problem of duplication is commingled with the pattern where compound strings are extensively used as keys.

Let's look at two examples:

Suppose we want to track the latencies for the REST API `GET /entity/{ownerId}/wiki2/{wikiId}/wikihistory`.  The bucket keys in Redis would look like the following:

    sum:hour:GET /entity/{ownerId}/wiki2/{wikiId}/wikihistory:1380754800
    sum:hour:GET /entity/{ownerId}/wiki2/{wikiId}/wikihistory:1380776400
    ...

To keep track of 3 months of activities, we will need 2230 such strings to store just the "hourly sum". If we also want to track `n` and `max` and daily metrics, the number of keys will grow to more than 10,000. To store 10,000 100-char strings in Java, we would need about 2 MB of memory. On a system with 50 REST APIs, the keys of the latency metric itself would take up 100 MB of memory. To add a 10-minute aggregation interval, the space requirement will go above 500 MB. It's a rough estimate but you get the sense of the scale.

Another example is the use of Redis sorted sets to count strings. Suppose we want to track the activities of different clients (the `user-agent` string):

    n:day:uniquecount:1380754800: {
        python-requests/1.2.3 cpython/2.7.1 linux/2.6.18-194.17.4.el5 : 3091
        synpase-java-client/11.0-5-gb11fbfa  synapse-web-client/12.0 : 490
        ...
    }

The Redis key looks reasonable here.  The problem is the keys, the user-agent strings, within the sorted set.  We need a set like this for each time bucket.  The long user-agent strings are duplicated in all the time buckets.

Long strings not only requires more storage, but also suffers from not-so-good performance with operations on them.  It takes longer to compare them, takes longer to compute hashes.  Not directly obvious, but long strings that vary only a little at the end is more easy to produce poor hashes to cause values to congregate.  In a distributed world, this means some hosts would store and handle more data than others.

### The solution ###

The solution is to swap the long strings with shorter, random strings.

Redis is in-memory cache, where accesses are fast and cheap but space is precious, comparing with spinning hard disks, I/Os via memory are cheaper but storage is more precious. 