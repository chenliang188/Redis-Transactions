### client ###

The set of all the clients. Example, "synapseRClient/0.29-1" is one such client.

### client:count:{interval}:{client} ###

Total number of calls made by a client at a fixed interval (1 hour, 1 day, or 1 week).

Key: {timestamp}. Note timestamp is calculated at the proper precision determined by the interval. 

Value: {count}

Queries:

* The counts for a particular client at a particular interval `HGETALL count:{interval}:{client}`. To restore the series, must sort on the timestamp after retrieval.

Notes:

* `KEYS client:count:1d:*` can fetch the list of clients at the 1 day interval, which can be used in place of the client set.
* Artificial method names can be added to represent aggregations of clients. For example, client:count:1d:python can be used to capture counts for all the python clients.

### client:count:{interval}:{method}:{yyyymmdd} ###

Total number of calls made by a client at a fixed interval (1 min, 5 min, or 10 min). This is the same as the above hash except that the counts are captured at smaller intervals.
