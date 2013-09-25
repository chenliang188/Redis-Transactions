
### method ###

The set of all the method calls. Example, "GET entity/{id}/bundle" is one such method call.

Type: SET

Key: {method}

Queries:

* The list of all the methods called: `SMEMBERS method`
### count:{interval}:{client} ###

Total number of calls made by a client at a fixed interval (1 hour, 1 day, or 1 week).

### count:{interval}:{client}:{day} ###

Total number of calls made by a client at a fixed interval (1 min, 5 min, or 10 min). This is the same as the above hash except that the counts are captured at smaller intervals.


### user ###

unique users using bitmap

### user:method

### user:client ###

### new projects ###

### projects updated ###

### project  ###

### most activate projects ###

### most active users ###

### files downloaded ###

(More will be added.)