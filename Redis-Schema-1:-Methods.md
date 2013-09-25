### method ###

The set of all the method calls. Example, "GET entity/{id}/bundle" is one such method call.

Type: SET

Key: {method}

Queries:

* The list of all the methods called: `SMEMBERS method`

### count:{interval}:{method} ###

Total number of calls to a method at a fixed interval (1 hour, 1 day, or 1 week).

Type: HASH

Key: {timestamp}. Note timestamp is calculated at the proper precision determined by the interval. 

Value: {count}

Queries:

* The counts for a particular method at a particular interval `HGETALL count:{interval}:{method}`. To restore the series, must sort on the timestamp after retrieved.

Notes:

* `KEYS count:60:*` can fetch the list of the methods at the 1 min interval, which can be used in place of the method set.
* Artificial method names can be added to represent aggregations of methods. For example, count:60:ALL can be used to capture counts for all the methods.

### count:{interval}:{method}:{day} ###

Total number of calls to a method at a fixed interval (1 min, 5 min, or 10 min). This is the same as the above hash except that the counts are captured at smaller intervals.

### latency-avg:{interval}:{method} ###

Average latency of a method call at a fixed interval (1 hour, 1 day, or 1 week).

### latency-tp50:{interval}:{method} ###

Median latency of a method call at a fixed interval (1 hour, 1 day, or 1 week).

### latency-tp90:{interval}:{method} ###

Tp90 latency of a method call at a fixed interval (1 hour, 1 day, or 1 week).

### latency-tp99:{interval}:{method} ###

Tp99 latency of a method call at a fixed interval (1 hour, 1 day, or 1 week).