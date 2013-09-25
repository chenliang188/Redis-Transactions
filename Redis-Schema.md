### method ###

The set of methods. Example, "GET entity/{id}/bundle".
Type: SET
Key: <method>
Queries:
* The list of all the methods called
`SMEMBERS method`

### count:method:\<interval\> ###

Description: Total number of calls to a method in 1 min, 5 min, 1 hour, 1 day, 1 week intervals.

Type: ZSET

Key: \<timestamp\>:\<method\>. Note timestamp is set at the proper precision determined by the interval. 

Value: \<count\>

### count:\<interval\>:\<method\> ###

Description: 

Type: ZSET

### count:\<interval\>:\<method\> ###

### latency ###

### latency:method ###

### latency:client ###

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