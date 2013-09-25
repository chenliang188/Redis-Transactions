### user:count:{yyyymmddhh} ###

The number of unique users at a particular hour.

Type: SET (used as a bit set. See <http://blog.getspool.com/2011/11/29/fast-easy-realtime-metrics-using-redis-bitmaps/>).  Alternatively, we can store a set of user IDs.

### user:new-account:{yyyymmddhh} ###

The set of new accounts at a particular hour.

Type: SET

Key/Value: {user-id}

Notes:

* Sets of different hours can be unioned.

### user:method:count ###

### user:client:count ###