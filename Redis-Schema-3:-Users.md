### user ###

The set of users.

Type: SET

Key/Value: {user-id}

### user:count:{yyyymmddhh} ###

The number of unique users at a particular hour.

Type: SET (One string used as a bit set. See <http://blog.getspool.com/2011/11/29/fast-easy-realtime-metrics-using-redis-bitmaps/>).  Alternatively, we can store a set of user IDs.

### user:new-account:{yyyymmddhh} ###

The set of new accounts at a particular hour.

Type: SET

Key/Value: {user-id}

Notes:

* Sets of different hours can be unioned.

### user:count:{interval} ###

The count of user activity at a fixed interval (1 hour, 1 day, or 1 week). This allows us to list the most active users.

Type: ZSET

Key: {user-id}

Value: {count}

### user:{user-id}:method:count:{interval} ###

Counts the use of method calls by a particular user at a fixed interval (1 day or 1 week).

Type: HASH

Key: {method}

Value: {count}

### user:{user-id}:client:count:{interval} ###

Counts the use of clients by a particular user at a fixed interval (1 day or 1 week).

Type: HASH

Key: {client}

Value: {count}