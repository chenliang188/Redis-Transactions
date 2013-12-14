### Count, Sum, Average, Max ###

#### Write ####

Let's assume we are measuring the latencies of method calls and we have the following influx of measurements:

| Method         | Latency (ms)  | Timestamp (posix)   |
| -------------- | ------------- | ------------------- |
| getEntity      | 300           | 1380755697          |
| getEntity      | 100           | 1380755695          |
| createEntity   | 1200          | 1380755702          |
| deleteEntity   | 30            | 1380779117          |
| ...            |               |                     |

One set of interesting metrics are aggregated time series where the statistics include:

    n    // Count. The number of samples.
    sum  // The sum of the samples.
    avg  // The average value of the samples.
    max  // The maximum value of the samples.

These statistics are typically summed over a fixed time interval such as every 3 minutes or every hour.

The essential idea here is to create a key for each unique time interval (aggregation bin) where the aggregation occurs. For example, if we want hourly metrics, we round down the timestamp to the nearest hour by doing integer division and multiplication.

    long hour = 60L * 60L;
    long key = (timestamp / hour) * hour;

In the example, the first entry's timestamp `1380755697` would be round down to the hour of `1380754800`. That's the essential idea. Then to help pinpoint the exact metric, we build a composite key on top of the round-down timestamp:

    {statistic}:{aggregation level}:{method name}:{timestamp by the hour}

An example of such a composite key would be `sum:hour:getEntity:1380754800` which holds the sum of getEntity latencies for the hour of `1380754800`. With keys like this, Redis commands [`INCR`](http://redis.io/commands/incr) (for updating count) and [`INCRBY`](http://redis.io/commands/incrby) (for updating sum) come in handy for updating the keys.

To increase the count by 1, we do this:

    redis> INCR key

To add to the sum, we do this:

    redis> INCRBY key amount

After the 4 entries are processed for hourly aggregation, we can expect the following key-value pairs in Redis:

| Key                              | Value    |
| -------------------------------- | ---------|
| n:hour:getEntity:1380754800      | 2        |
| sum:hour:getEntity:1380754800    | 400      |
| max:hour:getEntity:1380754800    | 300      |
| n:hour:createEntity:1380754800   | 1        |
| sum:hour:createEntity:1380754800 | 1200     |
| max:hour:createEntity:1380754800 | 1200     |
| n:hour:deleteEntity:1380776400   | 1        |
| sum:hour:deleteEntity:1380776400 | 30       |
| max:hour:deleteEntity:1380776400 | 30       |

#### Read ####

To query Redis for metrics over a time range, we compute the round-down timestamps the same and do a [`MGET`](http://redis.io/commands/mget) over the keys to get the list of values.

Using the same set of examples, to find the number of calls to `getEntity` on an hourly basis, we can do this:

    int hour = 60L * 60L;
    long start = 1380828662L;         // Starting point of yesterday
    start = (start * hour) / hour;    // Round-down is necessary in order to get the same timestamp
    long end = 1380859331L;           // Now
    List<Long> timestamps = new ArrayList<Long>();
    for (long timestamp = start; timestamp < end; timestamp += hour) {
        timestamps.add(timestamp);
    }
    String prefix = 'n:hour:genEntity:';
    List<String> keys = new ArrayList<String>();
    for (Long timestamp : timestamps) {
        keys.add(prefix + timestamp.toString());
    }
    // conn.mget(keys) to get the results