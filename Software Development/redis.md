# Redis
Redis (remote dictionary server) is a Data Structure Server because it supports various data structures and can act as a **Cache**, **Message Broker** and **Database**.

## Topics
1. [Data Types](#data-types)
    1. [String](#string)
    2. [Hash](#hash)
    3. [List](#list)
    4. [Set](#set)
    5. [Sorted Set](#sorted-set)
    6. [Bitmap](#bitmap)
    7. [Geospatial](#geospatial)
    8. [JSON](#json)
    9. [Time Series](#time-series)
2. [Probabilistic Data Types](#probabilistic-data-types)
    1. [HyperLogLog](#hyperloglog)
    2. [Bloom Filter](#bloom-filter)
    3. [Cuckoo Filter](#cuckoo-filter)
    4. [T-Digest](#t-digest)
    5. [Top-K](#top-k)
    6. [Count-Min](#count-min-sketch)
3. [Generic Commands](#generic-commands)
4. [Transactions](#transactions)
5. [Cluster Management](#cluster-management)
6. [Connection Management](#connection-management)
7. [Server Management](#server-management)

## Data Types
### String
- **APPEND**: If key already exists and is a string, this command appends the value at the end of the string. If key does not exist it is created and set with the specified value.
`APPEND key value`
- **DECR | INCR**: Decrements/Increments the number stored at key by one. If the key does not exist, it is set to 0 before performing the operation. An error is returned if the key contains a value of the wrong type or contains a string that can not be represented as integer. This operation is limited to 64 bit signed integers.
`DECR key` `INCR key`
- **DECR BY | INCR BY**: Reduces/Increases the value stored at the specified key by the specified decrement. If the key does not exist, it is initialized with a value of 0 before performing the operation. If the key's value is not of the correct type or cannot be represented as an integer, an error is returned. This operation is limited to 64-bit signed integers.
`DECRBY key decrement` `INCRBY key decrement`
- **GET**: Get the value of key. If the key does not exist the special value nil is returned. An error is returned if the value stored at key is not a string, because GET only handles string values.
`GET key`
- **GETDEL**: Get the value of key and delete the key. This command is similar to GET, except for the fact that it also deletes the key on success (if and only if the key's value type is a string).
`GETDEL key`
- **GETEX**: Get the value of key and optionally set its expiration. GETEX is similar to GET, but is a write command with additional options.
`GETEX key [EX seconds | PX milliseconds |
  EXAT unix-time-seconds | PXAT unix-time-milliseconds | PERSIST remove-ttl]`
- **GETRANGE**: Returns the substring of the string value stored at key, determined by the offsets start and end (both are inclusive). Negative offsets can be used in order to provide an offset starting from the end of the string. So -1 means the last character, -2 the penultimate and so forth. The function handles out of range requests by limiting the resulting range to the actual length of the string.
`GETRANGE key start end`
- **MGET**: Returns the values of all specified keys. For every key that does not hold a string value or does not exist, the special value nil is returned. Because of this, the operation never fails.
`MGET key [key ...]`
- **SET**: Set key to hold the string value. If key already holds a value, it is overwritten, regardless of its type. Any previous time to live associated with the key is discarded on successful SET operation.
`SET key value [NX | XX] [GET] [EX seconds | PX milliseconds |
  EXAT unix-time-seconds | PXAT unix-time-milliseconds | KEEPTTL]`
- **SETRANGE**: Overwrites part of the string stored at key, starting at the specified offset, for the entire length of value. If the offset is larger than the current length of the string at key, the string is padded with zero-bytes to make offset fit. Non-existing keys are considered as empty strings, so this command will make sure it holds a string large enough to be able to set value at offset.
`SETRANGE key offset value`
- **MSET**: Sets the given keys to their respective values. MSET replaces existing values with new values, just as regular SET. See MSETNX if you don't want to overwrite existing values. MSET is atomic, so all given keys are set at once. It is not possible for clients to see that some of the keys were updated while others are unchanged.
`MSET key value [key value ...]`
- **MSETNX**: Sets the given keys to their respective values. MSETNX will not perform any operation at all even if just a single key already exists.
`MSETNX key value [key value ...]`
- **STRLEN**: Returns the length of the string value stored at key. An error is returned when key holds a non-string value.
`STRLEN key`

### Hash
- **HDEL**: Removes the specified fields from the hash stored at key. Specified fields that do not exist within this hash are ignored. Deletes the hash if no fields remain. If key does not exist, it is treated as an empty hash and this command returns 0.
`HDEL key field [field ...]`
- **HEXISTS**: Returns if field is an existing field in the hash stored at key.
`HEXISTS key field`
- **HEXPIRE**: Set an expiration (TTL or time to live) on one or more fields of a given hash key. You must specify at least one field. Field(s) will automatically be deleted from the hash key when their TTLs expire.
`HEXPIRE key seconds [NX | XX | GT | LT] FIELDS numfields field
  [field ...]`
- **HEXPIREAT**: HEXPIREAT has the same effect and semantics as HEXPIRE, but instead of specifying the number of seconds for the TTL (time to live), it takes an absolute Unix timestamp in seconds since Unix epoch. A timestamp in the past will delete the field immediately.
`HEXPIREAT key unix-time-seconds [NX | XX | GT | LT] FIELDS numfields
  field [field ...]`
- **HEWPIRETIME**: Returns the absolute Unix timestamp in seconds since Unix epoch at which the given key's field(s) will expire.
`HEXPIRETIME key FIELDS numfields field [field ...]`
- **HSET**: Sets the specified fields to their respective values in the hash stored at key. This command overwrites the values of specified fields that exist in the hash. If key doesn't exist, a new key holding a hash is created.
`HSET key field value [field value ...]`
- **HSETEX**: Set the value of one or more fields of a given hash key, and optionally set their expiration time or time-to-live (TTL).
`HSETEX key [FNX | FXX] [EX seconds | PX milliseconds |
  EXAT unix-time-seconds | PXAT unix-time-milliseconds | KEEPTTL]
  FIELDS numfields field value [field value ...]`
- **HSETNX**: Sets field in the hash stored at key to value, only if field does not yet exist. If key does not exist, a new key holding a hash is created. If field already exists, this operation has no effect.
`HSETNX key field value`
- **HGET**: Returns the value associated with field in the hash stored at key.
`HGET key field`
- **HGETALL**: Returns all fields and values of the hash stored at key. In the returned value, every field name is followed by its value, so the length of the reply is twice the size of the hash.
`HGETALL key`
- **HGETDEL**: Get and delete the value of one or more fields of a given hash key. When the last field is deleted, the key will also be deleted.
`HGETDEL key FIELDS numfields field [field ...]`
- **HGETEX**: Get the value of one or more fields of a given hash key and optionally set their expiration time or time-to-live (TTL). 
`HGETEX key [EX seconds | PX milliseconds | EXAT unix-time-seconds |
  PXAT unix-time-milliseconds | PERSIST] FIELDS numfields field
  [field ...]`
- **HINCRBY**: Increments the number stored at field in the hash stored at key by increment. If key does not exist, a new key holding a hash is created. If field does not exist the value is set to 0 before the operation is performed. The range of values supported by HINCRBY is limited to 64 bit signed integers.
`HINCRBY key field increment`
- **HINCRBYFLOAT**: Increment the specified field of a hash stored at key, and representing a floating point number, by the specified increment. If the increment value is negative, the result is to have the hash field value decremented instead of incremented. If the field does not exist, it is set to 0 before performing the operation.
`HINCRBYFLOAT key field increment`
- **HKEYS**: Returns all field names in the hash stored at key.
`HKEYS key`
- **HLEN**: Returns the number of fields contained in the hash stored at key.
`HLEN key`
- **HMGET**: Returns the values associated with the specified fields in the hash stored at key. For every field that does not exist in the hash, a nil value is returned. Because non-existing keys are treated as empty hashes, running HMGET against a non-existing key will return a list of nil values.
`HMGET key field [field ...]`
- **HPERSIST**: Remove the existing expiration on a hash key's field(s), turning the field(s) from volatile (a field with expiration set) to persistent (a field that will never expire as no TTL (time to live) is associated).
`HPERSIST key FIELDS numfields field [field ...]`
- **HPEXPIRE**: This command works like HEXPIRE, but the expiration of a field is specified in milliseconds instead of seconds.
`HPEXPIRE key milliseconds [NX | XX | GT | LT] FIELDS numfields field
  [field ...]`
- **HPEXPIREAT**: HPEXPIREAT has the same effect and semantics as HEXPIREAT, but the Unix time at which the field will expire is specified in milliseconds since Unix epoch instead of seconds.
`HPEXPIREAT key unix-time-milliseconds [NX | XX | GT | LT]
  FIELDS numfields field [field ...]`
- **HPEXPIRETIME**: HPEXPIRETIME has the same semantics as HEXPIRETIME, but returns the absolute Unix expiration timestamp in milliseconds since Unix epoch instead of seconds.
`HPEXPIRETIME key FIELDS numfields field [field ...]`
- **HPTTL**: Like HTTL, this command returns the remaining TTL (time to live) of a field that has an expiration set, but in milliseconds instead of seconds.
`HPTTL key FIELDS numfields field [field ...]`
- **HTTL**: Returns the remaining TTL (time to live) of a hash key's field(s) that have a set expiration. This introspection capability allows you to check how many seconds a given hash field will continue to be part of the hash key.
`HTTL key FIELDS numfields field [field ...]`
- **HRANDFIELD**: When called with just the key argument, return a random field from the hash value stored at key. If the provided count argument is positive, return an array of distinct fields. The array's length is either count or the hash's number of fields (HLEN), whichever is lower. If called with a negative count, the behavior changes and the command is allowed to return the same field multiple times. In this case, the number of returned fields is the absolute value of the specified count. The optional WITHVALUES modifier changes the reply so it includes the respective values of the randomly selected hash fields.
`HRANDFIELD key [count [WITHVALUES]]`
- **HSCAN**: Iterates fields of Hash types and their associated values.
`HSCAN key cursor [MATCH pattern] [COUNT count] [NOVALUES]`
- **HSTRLEN**: Returns the string length of the value associated with field in the hash stored at key. If the key or the field do not exist, 0 is returned.
`HSTRLEN key field`
- **HVALS**: Returns all values in the hash stored at key.
`HVALS key`

### List
- **LINDEX**: Returns the element at index index in the list stored at key (0 indexed). Negative indices can be used to designate elements starting at the tail of the list. When the value at key is not a list, an error is returned.
`LINDEX key index`
- **LINSERT**: Inserts element in the list stored at key either before or after the reference value pivot. When key does not exist, it is considered an empty list and no operation is performed. An error is returned when key exists but does not hold a list value.
`LINSERT key <BEFORE | AFTER> pivot element`
- **LLEN**: Returns the length of the list stored at key. If key does not exist, it is interpreted as an empty list and 0 is returned. An error is returned when the value stored at key is not a list.
`LLEN key`
- **LMOVE**: Atomically returns and removes the first/last element (head/tail depending on the wherefrom argument) of the list stored at source, and pushes the element at the first/last element (head/tail depending on the whereto argument) of the list stored at destination. If source does not exist, the value nil is returned and no operation is performed.
`LMOVE source destination <LEFT | RIGHT> <LEFT | RIGHT>`
- **BLMOVE**: When source is empty, Redis will block the connection until another client pushes to it or until timeout (a double value specifying the maximum number of seconds to block) is reached. A timeout of zero can be used to block indefinitely.
`BLMOVE source destination <LEFT | RIGHT> <LEFT | RIGHT> timeout`
- **LPOP**: Removes and returns the first elements of the list stored at key.
`LPOP key [count]`
- **RPOP**: Removes and returns the last elements of the list stored at key.
`RPOP key [count]`
- **BLPOP**: An element is popped from the head of the first list that is non-empty, with the given keys being checked in the order that they are given.
`BLPOP key [key ...] timeout`
- **BRPOP**: Blocking variant of RPOP
`BRPOP key [key ...] timeout`
- **LMPOP**: Pops one or more elements from the first non-empty list key from the list of provided key names.
`LMPOP numkeys key [key ...] <LEFT | RIGHT> [COUNT count]`
- **BLMPOP**: When all lists are empty, Redis will block the connection until another client pushes to it or until the timeout (a double value specifying the maximum number of seconds to block) elapses. A timeout of zero can be used to block indefinitely.
`BLMPOP timeout numkeys key [key ...] <LEFT | RIGHT> [COUNT count]`
- **LPUSH**: Insert all the specified values at the head of the list stored at key. If key does not exist, it is created as empty list before performing the push operations. When key holds a value that is not a list, an error is returned.
`LPUSH key element [element ...]`
- **RPUSH**: Insert all the specified values at the tail of the list stored at key. If key does not exist, it is created as empty list before performing the push operation. When key holds a value that is not a list, an error is returned.
`RPUSH key element [element ...]`
- **LPUSHX**: Inserts specified values at the head of the list stored at key, only if key already exists and holds a list. 
`LPUSHX key element [element ...]`
- **RPUSHX**: Inserts specified values at the tail of the list stored at key, only if key already exists and holds a list. In contrary to RPUSH, no operation will be performed when key does not yet exist.
`RPUSHX key element [element ...]`
- **LPOS**: Returns the index of matching elements inside a Redis list. The RANK option specifies the "rank" of the first element to return (supports negative index). COUNT will try to return up to the specified number of matches.
`LPOS key element [RANK rank] [COUNT num-matches] [MAXLEN len]`
- **LRANGE**: Returns the specified elements of the list stored at key. The offsets start and stop are zero-based indexes and can be negative.If start is larger than the end of the list, an empty list is returned. If stop is larger than the actual end of the list, Redis will treat it like the last element of the list.
`LRANGE key start stop`
- **LREM**: Removes the first count occurrences of elements equal to element from the list stored at key. The count argument influences the operation in the following ways:
    - count > 0: Remove elements equal to element moving from head to tail.
    - count < 0: Remove elements equal to element moving from tail to head.
    - count = 0: Remove all elements equal to element.
`LREM key count element`
- **LSET**: Sets the list element at index to element. For more information on the index argument, see LINDEX. An error is returned for out of range indexes.
`LSET key index element`
- **LTRIM**: Trim an existing list so that it will contain only the specified range of elements specified ( 0-indexed and negative index supported). Out of range indexes will not produce an error.
`LTRIM key start stop`

### Set
- **SADD**: Add the specified members to the set stored at key. Specified members that are already a member of this set are ignored. If key does not exist, a new set is created before adding the specified members. An error is returned when the value stored at key is not a set.
`SADD key member [member ...]`
- **SREM**: Remove the specified members from the set stored at key. Specified members that are not a member of this set are ignored. If key does not exist, it is treated as an empty set and this command returns 0.
`SREM key member [member ...]`
- **SPOP**: By default, the command pops a single member from the set. When provided with the optional count argument, the reply will consist of up to count members, depending on the set's cardinality.
`SPOP key [count]`
- **SRANDMEMBER**: When called with just the key argument, return a random element from the set value stored at key. If the provided count argument is positive, return an array of distinct elements. The array's length is either count or the set's cardinality (SCARD), whichever is lower. If called with a negative count, the behavior changes and the command is allowed to return the same element multiple times. In this case, the number of returned elements is the absolute value of the specified count.
`SRANDMEMBER key [count]`
- **SMOVE**: Move member from the set at source to the set at destination. This operation is atomic. In every given moment the element will appear to be a member of source or destination for other clients. If the source set does not exist or does not contain the specified element, no operation is performed and 0 is returned. Otherwise, the element is removed from the source set and added to the destination set. When the specified element already exists in the destination set, it is only removed from the source set.
`SMOVE source destination member`
- **SCARD**: Returns the set cardinality (number of elements) of the set stored at key.
`SCARD key`
- **SUNION**: Returns the members of the set resulting from the union of all the given sets.
`SUNION key [key ...]`
- **SUNIONSTORE**: This command is equal to SUNION, but instead of returning the resulting set, it is stored in destination. If destination already exists, it is overwritten.
`SUNIONSTORE destination key [key ...]`
- **SDIFF**: Returns the members of the set resulting from the difference between the first set and all the successive sets.
`SDIFF key [key ...]`
- **SDIFFSTORE**: This command is equal to SDIFF, but instead of returning the resulting set, it is stored in destination. If destination already exists, it is overwritten.
`SDIFFSTORE destination key [key ...]`
- **SINTER**: Returns the members of the set resulting from the intersection of all the given sets.
`SINTER key [key ...]`
- **SINTERCARD**: Returns the cardinality of the set which would result from the intersection of all the given sets.
`SINTERCARD numkeys key [key ...] [LIMIT limit]`
- **SINTERSTORE**: This command is equal to SINTER, but instead of returning the resulting set, it is stored in destination. If destination already exists, it is overwritten.
`SINTERSTORE destination key [key ...]`
- **SISMEMBER**: Returns if member is a member of the set stored at key.
`SISMEMBER key member`
- **SMEMBERS**: Returns all the members of the set value stored at key. This has the same effect as running SINTER with one argument key.
`SMEMBERS key`
- **SMISMEMBERS**: Returns whether each member is a member of the set stored at key. For every member, 1 is returned if the value is a member of the set, or 0 if the element is not a member of the set or if key does not exist.
`SMISMEMBER key member [member ...]`

### Sorted set
- **ZADD**: Adds all the specified members with the specified scores to the sorted set stored at key. If a specified member is already a member of the sorted set, the score is updated and the element reinserted at the right position.
`ZADD score member [score member ...]`
- **ZCARD**: Returns the sorted set cardinality (number of elements) of the sorted set stored at key.
`ZCARD key`
- **ZCOUNT**: Returns the number of elements in the sorted set at key with a score between min and max.
`ZCOUNT key min max`
- **ZDIFF**: Computes the difference between the first and all successive input sorted sets and returns it.
`ZDIFF numkeys key [key ...]`
- **ZDIFFSTORE**: Same as ZDIFF but stores the result.
`ZDIFFSTORE destination numkeys key [key ...]`
- **ZINCRBY**: Increments the score of member in the sorted set stored at key by increment. If member does not exist in the sorted set, it is added with increment as its score.
`ZINCRBY key increment member`
- **ZINTER**: Computes the intersection of numkeys sorted sets given by the specified keys, and returns it.
`ZINTER numkeys key [key ...] `
- **ZINTERSTORE**: Same as ZINTER but stores the result instead.
`ZINTERSTORE destination numkeys key [key ...] `
- **ZINTERCARD**: Similar to ZINTER but returns the cardinality instead. 
`ZINTERCARD numkeys key [key ...]`
- **ZLEXCOUNT**: Returns the number of elements in the sorted set at key with a value between min and max.
`ZLEXCOUNT key min max`
- **ZMSCORE**: Returns the scores associated with the specified members in the sorted set stored at key. For every member that does not exist in the sorted set, a nil value is returned.
`ZMSCORE key member [member ...]`
- **ZMPOP**: Pops one or more elements, that are member-score pairs, from the first non-empty sorted set in the provided list of key names.
`ZMPOP numkeys key [key ...]`
- **ZMPOPMAX**: Removes and returns up to count members with the highest scores in the sorted set stored at key.
`ZPOPMAX key [count]`
- **ZMPOPMIN**: Removes and returns up to count members with the lowest scores in the sorted set stored at key.
`ZPOPMIN key [count]`
- **ZRANDMEMBER**: When called with just the key argument, return a random element from the sorted set value stored at key.
`ZRANDMEMBER key [count [WITHSCORES]]`
- **ZRANGE**: Returns the specified range of elements in the sorted set stored at key.
`ZRANGE key start stop [BYSCORE | BYLEX] [REV] [LIMIT offset count] [WITHSCORES]`
- **ZRANGESTORE**: Returns all the elements in the sorted set at key with a score between min and max (ordered by high scores).
`ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT offset count]`
- **ZRANK**: Returns the rank of member in the sorted set stored at key, with the scores ordered from low to high.
`ZRANK key member [WITHSCORE]`
- **ZREM**: Removes the specified members from the sorted set stored at key. Non existing members are ignored.
`ZREM key member [member ...]`
- **ZREMRANGEBYLEX**: Removes all elements in the sorted set stored at key between the lexicographical range specified by min and max.
`ZREMRANGEBYLEX key min max`
- **ZREMRANGEBYRANK**: Removes all elements in the sorted set stored at key with rank between start and stop.
`ZREMRANGEBYRANK key start stop`
- **ZREMRANGEBYSCORE**: Removes all elements in the sorted set stored at key with a score between min and max.
`ZREMRANGEBYSCORE key min max`
- **ZREVRANK**: Returns the rank of member in the sorted set stored at key, with the scores ordered from high to low.
`ZREVRANK key member [WITHSCORE]`
- **ZSCAN**: Scans and returns the content.
`ZSCAN key cursor [MATCH pattern] [COUNT count]`
- **ZSCORE**: Returns the score of member in the sorted set at key or nil if it doesnt exist.
`ZSCORE key member`
- **ZUNION**: Computes the union of numkeys sorted sets given by the specified keys, and returns it.
`ZUNION numkeys key [key ...]`
- **ZUNIONSTORE**: Same as ZUNION except it stores the results.
`ZUNIONSTORE destination numkeys key [key ...]`
- **BZMPOP**: Blocking variant of ZMPOP.
`BZMPOP timeout numkeys key [key ...]`
- **BZMPOPMAX**: Blocking variant of ZMPOPMAX.
`BZPOPMAX key [key ...] timeout`
- **BZMPOPMIN**: Blocking variant of ZMPOPMIN.
`BZPOPMIN key [key ...] timeout`

### Bitmap
- **BICOUNT**: Count the number of set bits (population counting) in a string.
`BITCOUNT key [start end [BYTE | BIT]]`
- **BITFIELD**: Treats a Redis string as an array of bits and allows oprating on it.
`BITFIELD key [GET encoding offset | [OVERFLOW <WRAP | SAT | FAIL>] <SET encoding offset value | INCRBY encoding offset increment>`
- **BIFIELD_RO**: Read only variant of BITFIELD.
`BITFIELD_RO key [GET encoding offset [GET encoding offset ...]]`
- **BITOP**: Perform a bitwise operation between multiple keys (containing string values) and store the result in the destination key.
`BITOP <AND | OR | XOR | NOT | DIFF | DIFF1 | ANDOR | ONE> destkey key [key ...]`
- **BITPOS**: Return the position of the first bit set to 1 or 0 in a string.
`BITPOS key bit [start [end [BYTE | BIT]]]`
- **GETBIT**: Returns the bit value at offset in the string value stored at key.
`GETBIT key offset`
- **SETBIT**: Sets or clears the bit at offset in the string value stored at key.
`SETBIT key offset value`

### Geospatial
- **GEOADD**: Adds the specified geospatial items (longitude, latitude, name) to the specified key. Data is stored into the key as a sorted set.
`GEOADD key longitude latitude member [longitude
  latitude member ...]`
- **GEODIST**: Return the distance between two members in the geospatial index represented by the sorted set.
`GEODIST key member1 member2 [M | KM | FT | MI]`
- **GEOHASH**: Return valid Geohash strings representing the position of one or more elements in a sorted set value representing a geospatial index.
`GEOHASH key [member [member ...]]`
- **GEOPOS**: Return the positions (longitude,latitude) of all the specified members of the geospatial index represented by the sorted set at key.
`GEOPOS key [member [member ...]]`
- **GEOSEARCH**: Return the members of a sorted set populated with geospatial information using GEOADD, which are within the borders of the area specified by a given shape.
`GEOSEARCH key <FROMMEMBER member | FROMLONLAT longitude latitude>
  <BYRADIUS radius <M | KM | FT | MI> | BYBOX width height <M | KM |
  FT | MI>> [ASC | DESC] [COUNT count [ANY]] [WITHCOORD] [WITHDIST]
  [WITHHASH]`
- **GEOSEARCHSTORE**: Like GEOSEARCH, but stores the result in destination key.
`GEOSEARCHSTORE destination source <FROMMEMBER member |
  FROMLONLAT longitude latitude> <BYRADIUS radius <M | KM | FT | MI>
  | BYBOX width height <M | KM | FT | MI>> [ASC | DESC] [COUNT count
  [ANY]] [STOREDIST]`

### JSON 
- **JSON.ARRAPPEND**: Append the JSON values into the array at path after the last element in it.
`JSON.ARRAPPEND key path value [value ...]`
- **JSON.ARRINDEX**: Search for the first occurrence of a JSON value in an array.
`JSON.ARRINDEX key path value [start [stop]]`
- **JSON.ARRINSERT**: Insert the json values into the array at path before the index (shifts to the right).
`JSON.ARRINSERT key path index value [value ...]`
- **JSON.ARRLEN**: Report the length of the JSON array at path in key.
`JSON.ARRLEN key [path]`
- **JSON.ARRPOP**: Remove and return an element from the index in the array.
`JSON.ARRPOP key [path [index]]`
- **JSON.ARRTRIM**: Trim an array so that it contains only the specified inclusive range of elements.
`JSON.ARRTRIM key path start stop`
- **JSON.CLEAR**: Clear container values (arrays/objects) and set numeric values to 0.
`JSON.CLEAR key [path]`
- **JSON.DEL**: Delete a value.
`JSON.DEL key [path]`
- **JSON.FORGET**: Same as JSON.DEL
`JSON.FORGET key [path]`
- **JSON.GET**: Return the value at path in JSON serialized form.
`JSON.GET key [INDENT indent] [NEWLINE newline] [SPACE space] [path [path ...]]`
- **JSON.MERGE**: Merge a given JSON value into matching paths. Consequently, JSON values at matching paths are updated, deleted, or expanded with new children.
`JSON.MERGE key path value`
- **JSON.MGET**: Return the values at path from multiple key arguments.
`JSON.MGET key [key ...] path`
- **JSON.MSET**: Set or update one or more JSON values according to the specified key-path-value triplets. JSON.MSET is atomic, hence, all given additions or updates are either applied or not. It is not possible for clients to see that some of the keys were updated while others are unchanged.
`JSON.MSET key path value [key path value ...]`
- **JSON.NUMINCRBY**: Increment the number value stored at path by number.
`JSON.NUMINCRBY key path value`
- **JSON.NUMMULTBY**: Multiply the number value stored at path by number.
`JSON.NUMMULTBY key path value`
- **JSON.OBJKEYS**: Return the keys in the object that's referenced by path.
`JSON.OBJKEYS key [path]`
- **JSON.OBJLEN**: Report the number of keys in the JSON object at path in key.
`JSON.OBJLEN key [path]`
- **JSON.SET**: Set the JSON value at path in key.
`JSON.SET key path value [NX | XX]`
- **JSON.STRAPPEND**: Append the json-string values to the string at path.
`JSON.STRAPPEND key [path] value`
- **JSON.STRLEN**: Report the length of the JSON String at path in key.
`JSON.STRLEN key [path]`
- **JSON.TOGGLE**: Toggle a Boolean value stored at path.
`JSON.TOGGLE key path`
- **JSON.TYPE**: Report the type of JSON value at path.
`JSON.TYPE key [path]`

### Time series
- **TS.ADD**: Append a sample to a time series
`TS.ADD key timestamp value 
  [RETENTION retentionPeriod] 
  [ENCODING <COMPRESSED|UNCOMPRESSED>] 
  [CHUNK_SIZE size] 
  [DUPLICATE_POLICY policy] 
  [ON_DUPLICATE policy_ovr] 
  [IGNORE ignoreMaxTimediff ignoreMaxValDiff] 
  [LABELS [label value ...]]`
- **TS.ALTER**: Update the retention, chunk size, duplicate policy, and labels of an existing time series.
`TS.ALTER key 
  [RETENTION retentionPeriod] 
  [CHUNK_SIZE size] 
  [DUPLICATE_POLICY policy] 
  [IGNORE ignoreMaxTimediff ignoreMaxValDiff] 
  [LABELS [label value ...]]`
- **TS.CREATE**: Create a new time series.
`TS.CREATE key 
  [RETENTION retentionPeriod] 
  [ENCODING <COMPRESSED|UNCOMPRESSED>] 
  [CHUNK_SIZE size] 
  [DUPLICATE_POLICY policy] 
  [IGNORE ignoreMaxTimediff ignoreMaxValDiff] 
  [LABELS [label value ...]]`
- **TS.CREATERULE**: Create a compaction rule.
`TS.CREATERULE sourceKey destKey 
  AGGREGATION aggregator bucketDuration 
  [alignTimestamp]`
- **TS.DECRBY**: Decrease the value of the sample with the maximum existing timestamp, or create a new sample with a value equal to the value of the sample with the maximum existing timestamp with a given decrement.
`TS.DECRBY key subtrahend 
  [TIMESTAMP timestamp] 
  [RETENTION retentionPeriod] 
  [ENCODING <COMPRESSED|UNCOMPRESSED>] 
  [CHUNK_SIZE size] 
  [DUPLICATE_POLICY policy] 
  [IGNORE ignoreMaxTimediff ignoreMaxValDiff]  
  [LABELS [label value ...]]`
- **TS.DEL**: Delete all samples between two timestamps for a given time series.
`TS.DEL key fromTimestamp toTimestamp`
- **TS.DELETERULE**: Delete a compaction rule.
`TS.DELETERULE sourceKey destKey`
- **TS.GET**: Get the sample with the highest timestamp from a given time series
`TS.GET key [LATEST]`
- **TS.INCRBY**: Increase the value of the sample with the maximum existing timestamp, or create a new sample with a value equal to the value of the sample with the maximum existing timestamp with a given increment
`TS.INCRBY key addend 
  [TIMESTAMP timestamp] 
  [RETENTION retentionPeriod] 
  [ENCODING <COMPRESSED|UNCOMPRESSED>] 
  [CHUNK_SIZE size] 
  [DUPLICATE_POLICY policy] 
  [IGNORE ignoreMaxTimediff ignoreMaxValDiff]   
  [LABELS [label value ...]]`
- **TS.INFO**: Return information and statistics for a time series.
`TS.INFO key [DEBUG]`
- **TS.MADD**: Append new samples to one or more time series.
`TS.MADD {key timestamp value}...`
- **TS.MGET**: Get the sample with the highest timestamp from each time series matching a specific filter.
`TS.MGET [LATEST] [WITHLABELS | <SELECTED_LABELS label...>] FILTER filterExpr...`
- **TS.MRANGE**: Query a range across multiple time series by filters in the forward direction.
`TS.MRANGE fromTimestamp toTimestamp
  [LATEST]
  [FILTER_BY_TS ts...]
  [FILTER_BY_VALUE min max]
  [WITHLABELS | <SELECTED_LABELS label...>]
  [COUNT count]
  [[ALIGN align] AGGREGATION aggregator bucketDuration [BUCKETTIMESTAMP bt] [EMPTY]]
  FILTER filterExpr...
  [GROUPBY label REDUCE reducer]`
- **TS.MREVRANGE**: Query a range across multiple time series by filters in the reverse direction.
`TS.MREVRANGE fromTimestamp toTimestamp
  [LATEST] [FILTER_BY_TS ts...] [FILTER_BY_VALUE min max]
  [WITHLABELS | <SELECTED_LABELS label...>]
  [COUNT count]
  [[ALIGN align] AGGREGATION aggregator bucketDuration [BUCKETTIMESTAMP bt] [EMPTY]]
  FILTER filterExpr...
  [GROUPBY label REDUCE reducer]`
- **TS.QUERYINDEX**: Get all time series keys matching a filter list. Note: all matching keys will be listed, whether or not the user has read access.
`TS.QUERYINDEX filterExpr...`
- **TS.RANGE**: Query a range in forward direction
`TS.RANGE key fromTimestamp toTimestamp
  [LATEST]
  [FILTER_BY_TS ts...]
  [FILTER_BY_VALUE min max]
  [COUNT count] 
  [[ALIGN align] AGGREGATION aggregator bucketDuration [BUCKETTIMESTAMP bt] [EMPTY]]`
- **TS.REVRANGE**: Query a range in reverse direction
`TS.REVRANGE key fromTimestamp toTimestamp
  [LATEST]
  [FILTER_BY_TS ts...]
  [FILTER_BY_VALUE min max]
  [COUNT count]
  [[ALIGN align] AGGREGATION aggregator bucketDuration [BUCKETTIMESTAMP bt] [EMPTY]]`

## Probabilistic data types
### HyperLogLog
The HyperLogLog (HLL) algorithm is a probabilistic data structure used to estimate the number of distinct elements (cardinality) in a very large dataset or stream using a fixed, small amount of memory.
- **PFADD**: Adds all the element arguments to the HyperLogLog data structure stored at the variable name specified as first argument.
`PFADD key [element [element ...]]`
- **PFCOUNT**: When called with a single key, returns the approximated cardinality computed by the HyperLogLog data structure stored at the specified variable, which is 0 if the variable does not exist. When called with multiple keys, returns the approximated cardinality of the union of the HyperLogLogs passed, by internally merging the HyperLogLogs stored at the provided keys into a temporary HyperLogLog.
`PFCOUNT key [key ...]`
- **PFMERGE**: Merge multiple HyperLogLog values into a unique value that will approximate the cardinality of the union of the observed Sets of the source HyperLogLog structures.
`PFMERGE destkey [sourcekey [sourcekey ...]]`

### Bloom filter
A Bloom filter is a space-efficient probabilistic data structure used to test whether an element is a member of a set.
- **BF.ADD**: Adds an item to a Bloom filter.
`BF.ADD key item`
- **BF.MADD**: Adds one or more items to a Bloom filter.
`BF.MADD key item [item ...]`
- **BF.CARD**: Returns the cardinality of a Bloom filter.
`BF.CARD key`
- **BF.EXISTS**: Determines whether a given item was added to a Bloom filter.
`BF.EXISTS key item`
- **BF.MEXISTS**: Determines whether one or more items were added to a Bloom filter.
`BF.EXISTS key item [item ...]`
- **BF.INFO**: Returns information about a Bloom filter.
`BF.INFO key [CAPACITY | SIZE | FILTERS | ITEMS | EXPANSION]`
- **BF.INSERT**: Creates a new Bloom filter if the key does not exist using the specified error rate, capacity, and expansion, then adds all specified items to the Bloom Filter.
`BF.INSERT key [CAPACITY capacity] [ERROR error] [EXPANSION expansion] [NOCREATE] [NONSCALING] ITEMS item [item ...]`
- **BF.LOADCHUNK**: Restores a Bloom filter previously saved using BF.SCANDUMP.
`BF.LOADCHUNK key iterator data`
- **BF.RESERVE**: Creates an empty Bloom filter with a single sub-filter for the initial specified capacity and with an upper bound error_rate.
`BF.RESERVE key error_rate capacity [EXPANSION expansion] [NONSCALING]`
- **BF.SCANDUMP**: Begins an incremental save of the Bloom filter.
`BF.SCANDUMP key iterator`

### Cuckoo filter
A cuckoo filter is a space-efficient probabilistic data structure used for approximate set-membership tests
- **CF.ADD**: Adds an item to the cuckoo filter.
`CF.ADD key item`
- **CF.ADDNX**: Adds an item to a cuckoo filter if the item does not exist.
`CF.ADDNX key item`
- **CF.COUNT**: Returns an estimation of the number of times a given item was added to a cuckoo filter.
`CF.COUNT key item`
- **CF.DEL**: If the item exists only once, it will be removed from the filter. If the item was added multiple times, it will still be present.
`CF.DEL key item`
- **CF.EXISTS**: Determines whether a given item was added to a cuckoo filter.
`CF.EXISTS key item`
- **CF.MEXISTS**: Determines whether one or more items were added to a cuckoo filter.
`CF.MEXISTS key item [item ...]`
- **CF.INFO**: Returns information about a cuckoo filter.
`CF.INFO key`
- **CF.INSERT**: Adds one or more items to a cuckoo filter, allowing the filter to be created with a custom capacity if it does not exist yet.
`CF.INSERT key [CAPACITY capacity] [NOCREATE] ITEMS item [item ...]`
- **CF.INSERTNX**: Adds one or more items to a cuckoo filter if they did not exist previously, allowing the filter to be created with a custom capacity if it does not exist yet.
`CF.INSERTNX key [CAPACITY capacity] [NOCREATE] ITEMS item [item ...]`
- **CF.LOADCHUNK**: Restores a cuckoo filter previously saved using CF.SCANDUMP.
`CF.LOADCHUNK key iterator data`
- **CF.RESERVE**: Creates an empty cuckoo filter with a single sub-filter for the initial specified capacity.
`CF.RESERVE key capacity [BUCKETSIZE bucketsize] [MAXITERATIONS maxiterations] [EXPANSION expansion]`
- **CF.SCANDUMP**: Begins an incremental save of the cuckoo filter.
`CF.SCANDUMP key iterator`

### T-Digest
The term "t-digest" refers to a probabilistic data structure and algorithm used for estimating quantiles (percentiles, median, etc.) of a large or streaming dataset with high accuracy and a small memory footprint.
- **TDIGEST.ADD**: Adds one or more observations to a t-digest sketch.
`TDIGEST.ADD key value [value ...]`
- **TDIGEST.BYRANK**: Returns, for each input rank, a floating-point estimation of the value with that rank. Multiple estimations can be retrieved in a single call.
`TDIGEST.BYRANK key rank [rank ...]`
- **TDIGEST.BYREVRANK**: Returns, for each input reverse rank (revrank), an estimation of the floating-point value with that reverse rank. Multiple estimations can be retrieved in a single call.
`TDIGEST.BYREVRANK key reverse_rank [reverse_rank ...]`
- **TDIGEST.CDF**: Returns, for each input value, an estimation of the floating-point fraction of (observations smaller than the given value + half the observations equal to the given value). Multiple fractions can be retrieved in a single call.
`TDIGEST.CDF key value [value ...]`
- **TDIGEST.CREATE**: Allocates memory and initializes a new t-digest sketch.
`TDIGEST.CREATE key [COMPRESSION compression]`
- **TDIGEST.INFO**: Returns information and statistics about a t-digest sketch.
`TDIGEST.INFO key`
- **TDIGEST.MAX**: Returns the maximum observation value from a t-digest sketch.
`TDIGEST.MAX key`
- **TDIGEST.MERGE**: Merges multiple t-digest sketches into a single sketch.
`TDIGEST.MERGE destination-key numkeys source-key [source-key ...] [COMPRESSION compression] [OVERRIDE]`
- **TDIGEST.MIN**: Returns the minimum observation value from a t-digest sketch.
`TDIGEST.MIN key`
- **TDIGEST.QUANTILE**: Returns, for each input fraction, a floating-point estimation of the value that is smaller than the given fraction of observations. Multiple quantiles can be retrieved in a single call.
`TDIGEST.QUANTILE key quantile [quantile ...]`
- **TDIGEST.RANK**: Returns, for each floating-point input value, the estimated rank of the value (the number of observations in the sketch that are smaller than the value + half the number of observations that are equal to the value). Multiple ranks can be retrieved in a single call.
`TDIGEST.RANK key value [value ...]`
- **TDIGEST.RESET**: Resets a t-digest sketch: empties the sketch and re-initializes it.
`TDIGEST.RESET key`
- **TDIGEST.REVRANK**: Returns, for each floating-point input value, the estimated reverse rank of the value (the number of observations in the sketch that are larger than the value + half the number of observations that are equal to the value). Multiple reverse ranks can be retrieved in a single call.
`TDIGEST.REVRANK key value [value ...]`
- **TDIGEST.TRIMMED_MEAN**: Returns an estimation of the mean value from the sketch, excluding observation values outside the low and high cutoff quantiles.
`TDIGEST.TRIMMED_MEAN key low_cut_quantile high_cut_quantile`

### Top-K
A "top-k sketch" is a probabilistic data structure used to find the top k most frequent or highest-scoring items in a data stream with limited memory
- **TOPK.ADD**: Adds an item to a Top-k sketch. Multiple items can be added at the same time. If an item enters the Top-K sketch, the item that is expelled (if any) is returned.
`TOPK.ADD key items [items ...]`
- **TOPK.COUNT**: Returns counts for each item present in the sketch. Multiple items can be requested at once.
`TOPK.COUNT key item [item ...]`
- **TOPK.INCRBY**: Increase the score of an item in the data structure by increment. Multiple items' scores can be increased at once. If an item enters the Top-K list, the item that is expelled (if any) is returned.
`TOPK.INCRBY key item increment [item increment ...]`
- **TOPK.INFO**: Returns number of required items (k), width, depth, and decay values of a given sketch.
`TOPK.INFO key`
- **TOPK.LIST**: Return the full list of items in Top-K sketch.
`TOPK.LIST key [WITHCOUNT]`
- **TOPK.QUERY**: Checks whether one or more items are one of the Top-K items.
`TOPK.QUERY key item [item ...]`
- **TOPK.RESERVE**: Initializes a Top-K sketch with specified parameters.
`TOPK.RESERVE key topk [width depth decay]`

### Count-min sketch
Count-Min sketch (CM sketch), which is a probabilistic data structure used to estimate the frequencies of elements in a large data stream using limited memory
- **CMS.INCRBY**: Increases the count of item by increment. Multiple items can be increased with one call.
`CMS.INCRBY key item increment [item increment ...]`
- **CMS.INFO**: Returns width, depth and total count of the sketch.
`CMS.INFO key`
- **CMS.INITBYDIM**: Initializes a Count-Min Sketch to dimensions specified by user.
`CMS.INITBYDIM key width depth`
- **CMS.INITBYPROB**: Initializes a Count-Min Sketch to accommodate requested tolerances.
`CMS.INITBYPROB key error probability`
- **CMS.MERGE**: Merges several sketches into one sketch. All sketches must have identical width and depth. Weights can be used to multiply certain sketches. Default weight is 1.
`CMS.MERGE destination numKeys source [source ...] [WEIGHTS weight [weight ...]]`
- **CMS.QUERY**: Returns the count for one or more items in a sketch.
`CMS.QUERY key item [item ...]`

## Transactions
Redis Transactions allow the execution of a group of commands in a single step.
- **DISCARD**: Flushes all previously queued commands in a transaction and restores the connection state to normal. If WATCH was used, DISCARD unwatches all keys watched by the connection.
- **EXEC**: Executes all previously queued commands in a transaction and restores the connection state to normal. When using WATCH, EXEC will execute commands only if the watched keys were not modified, allowing for a check-and-set mechanism.
- **MULTI**: Marks the start of a transaction block. Subsequent commands will be queued for atomic execution using EXEC.
- **UNWATCH**: Flushes all the previously watched keys for a transaction. If you call EXEC or DISCARD, there's no need to manually call UNWATCH.
- **WATCH**: Marks the given keys to be watched for conditional execution of a transaction.

## Pub/Sub
- **PUBLISH**: Posts a message to the given channel.
`PUBLISH channel message`
- **PUBSUB CHANNELS**: Lists the currently active channels. An active channel is a Pub/Sub channel with one or more subscribers (excluding clients subscribed to patterns). If no pattern is specified, all the channels are listed, otherwise if pattern is specified only channels matching the specified glob-style pattern are listed.
`PUBSUB CHANNELS [pattern]`
- **PUBSUB NUMPAT**: Returns the number of unique patterns that are subscribed to by clients (that are performed using the PSUBSCRIBE command). Note that this isn't the count of clients subscribed to patterns, but the total number of unique patterns all the clients are subscribed to.
`PUBSUB NUMPAT`
- **PUBSUB NUMSUB**: Returns the number of subscribers (exclusive of clients subscribed to patterns) for the specified channels.
`PUBSUB NUMSUB [channel [channel ...]]`
- **SUBSCRIBE**: Subscribes the client to the specified channels.
`SUBSCRIBE channel [channel ...]`
- **UNSUBSCRIBE**: Unsubscribes the client from the given channels, or from all of them if none is given. When no channels are specified, the client is unsubscribed from all the previously subscribed channels. In this case, a message for every unsubscribed channel will be sent to the client.
`UNSUBSCRIBE [channel [channel ...]]`
- **SPUBLISH**: Posts a message to the given  channel.
`SPUBLISH channel message`
- **PSUBSCRIBE**: Subscribes the client to the given patterns.
`PSUBSCRIBE pattern [pattern ...]`
- **PUNSUBSCRIBE**: Unsubscribes the client from the given patterns, or from all of them if none is given.
`PUNSUBSCRIBE [pattern [pattern ...]]`

### Shard Channels
In Redis Cluster, shard channels are assigned to slots by the same algorithm used to assign keys to slots. A shard message must be sent to a node that owns the slot the shard channel is hashed to. The cluster makes sure that published shard messages are forwarded to all the nodes in the shard, so clients can subscribe to a shard channel by connecting to any one of the nodes in the shard.
- **SSUBSCRIBE**: Subscribes the client to the specified  channels.
`SSUBSCRIBE channel [shardchannel ...]`
- **SUNSUBCRIBE**: Unsubscribes the client from the given  channels, or from all of them if none is given. When no shard channels are specified, the client is unsubscribed from all the previously subscribed shard channels. In this case a message for every unsubscribed shard channel will be sent to the client.
`SUNSUBSCRIBE [channel [shardchannel ...]]`
- **PUBSUB SHARDCHANNELS**: Lists the currently active  channels. An active shard channel is a Pub/Sub shard channel with one or more subscribers. If no pattern is specified, all the channels are listed, otherwise if pattern is specified only channels matching the specified glob-style pattern are listed. The information returned about the active shard channels are at the shard level and not at the cluster level.
`PUBSUB SHARDCHANNELS [pattern]`
- **PUBSUB SHARDBUMSUB**: Returns the number of subscribers for the specified  channels. Note that it is valid to call this command without channels, in this case it will just return an empty list.
`PUBSUB SHARDNUMSUB [channel [shardchannel ...]]`

## Generic commands
- ****:
``

## Cluster Management

## Connection Management

## Server Management
