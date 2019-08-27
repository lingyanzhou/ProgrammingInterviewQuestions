# Redis

## What is Redis 

Redis is an open source, in-memory data structure store, it can be used as a database, cache and message broker.

## Redis Thread Model 

- 使用多个socket监听
- 使用IO多路复用处理IO事件, 并添加到一个事件队列
- 文件处理分派器获取事件, 并将事件交给事件处理器
- 事件处理器（连接应答处理器、命令请求处理器、命令回复处理器）

![Redis Thread Model](../img/redis-threading.png)

## Reasons for Performance 
   
- In-memory database,
- IO Multiplexing,
- No context switch and lock.

[redis面试必问（上）](https://www.jianshu.com/p/f8ab9a75c1e0)

## Redis基础数据结构

- String

  Redis string is a sequence of bytes. Strings in Redis are binary safe. 
  
  Strings can store 3 types:
  
  - byte strings (Bitmaps),
  - integers,
  - floating point values.
  
  *Note*: A string value can be at max 512 megabytes in length.

- List

  Redis Lists are simply lists of strings, sorted by insertion order. You can add elements to a Redis List on the head or on the tail.
  
  *Note*: The max number of members in a list is `2^32 - 1`.  

- Hash

  A Redis hash is a collection of key value pairs.
  
  *Note*: Every hash can store up to `2^32 - 1` field-value pairs.

- Set

  Redis Sets are an unordered collection of strings. In Redis, you can add, remove, and test for the existence of members in O(1) time complexity.
  
  *Note*: The max number of members in a set is `2^32 - 1`.

- ZSet

  Every element in a sorted set is associated with a floating point value. Elements are ordered in the following rules:

  - If A and B are two elements with a different score, then A > B if A.score is > B.score.
  - If A and B have exactly the same score, then A > B if the A string is lexicographically greater than the B string. A and B strings can't be equal since sorted sets only have unique elements.


## Redis高级数据结构

- Hyperloglog

  HyperLogLog provides a very good approximation of the cardinality of a set even using a very small amount of memory around 12 kbytes per key with a standard error of 0.81%.

  *Note* : The maximum cardinality is approximately 2^64.
  
- Pub/Sub

- Geo

[Redis - Data Types](http://www.tutorialspoint.com/redis/redis_data_types.htm)

## Redis Key Command

|     Command                |  Description    |
| `DEL <key>`                |  Deletes the key, if it exists.  |
| `EXISTS <key>`             | Checks whether the key exists or not. |
| `MOVE <key> <db>`          | Moves a key to another database. |
| `RENAME <key> <newKey>`    | Changes the key name.  |
| `RENAMENX <key> <newKey>`  | Renames the key, if the new key doesn't exist. |
| `TYPE <key>`               | Returns the data type of the value stored in the key. |

## Redis String Command

- Byte string operations:

|     Command                |  Description    |
|----------------------------|----------------------|
| `GET <key>`                |  Gets the value.  |
| `SET <key> <value>`                | Sets the value. |
| `MGET <key> [<value> ...]`                |  Gets the values.  |
| `MSET <key> <value> [<key> <value> ...]` | Sets the values. |
| `GETSET <key> <value>`     | Sets the value and returns the old value. |
| `STRLEN <key>`             | Gets the string length.  |
| `APPEND <key> <value>`          | Appends a value. |
| `GETRANGE <key> <start> <end>`    | Gets a substring of the string. Indices start from 0, and are inclusive. |
| `SETRANGE <key> <offset> <value>`  | Overwrites the part of a string at the key starting at the specified offset. |
| `SETNX <key> <value>`      | Sets the value if the key does not exist. |

*Note*: Substring outside the byte string will be considered as an empty string.

- Bitmap operations:.

|     Command                |  Description    |
|----------------------------|----------------------|
| `GETBIT <key> <offset>`                |  Gets the bit value at the offset. |
| `SETBIT <key> <offset> <value>`             | Sets the bit value at the offset. |
| `BITCOUNT <key> [<start> <end>]`          | Count the number of 1-bit in the string (or in the specified range). |
| `BITOP <key> <operation> <dest-key> <key> [<key> ...]`    | Applies bitwise operation (AND, OR, XOR, NOT) to specified keys, and stores the result in the `<dest-key>`. |

*Note*: Bits outside the bitmap will be considered as 0.

- Integer / floating point operations:

|     Command             |  Description    |
|-------------------------|----------------------|
| `INCR <key>`              | Increments the value by 1; returns the new value.  |
| `DECR <key>`              | Decrements the value by 1; returns the new value. |
| `INCRBY <key> <amount>`              | Increments the value by integer value `<amount>`; returns the new value. |
| `DECRBY <key> <amount>`              | Decrements the value by integer value `<amount>`; returns the new value. |
| `INCRBYFLOAT <key> <amount>`              |  Increments the value by float value `<amount>`; returns the new value.  |

- String operations:

|     Command             |  Description    |
|-------------------------|----------------------|

## Redis List Commands

- Non-blocking commands

|     Command             |  Description    |
|-------------------------|----------------------|
| `RPUSH <key> <value> [<value> ...]`   | Pushes the values to the right (tail) of the list. |
| `LPUSH <key> <value> [<value> ...]`   | Pushes the values to the left (head) of the list. |
| `RPOP <key>`                          | Remove and retrieves the value from the right of the list. |
| `LPOP <key>`                          | Remove and retrieves the value from the left of the list. |
| `LINDEX <key> <offset>`               | Retrieves the element at the index. |
| `LRANGE <key> <start> <end>`          | Gets the sublist from the specified range. |
| `LTRIM <key> <start> <end>`           | Trims the list. Keeps the elements in the specified range (inclusive). |
| `RPOPLPUSH <source-key> <dest-key>`   | Removes the rightmost value from `<source-key>`; push the value to the left of `<dest-key>`; and retrieves the value. If `<source-key>` is empty, nothing happens. |

*Note*: When the list is empty, these commands return `Nil`.

- Blocking commands

|     Command                                      |  Description    |
|--------------------------------------------------|----------------------|
| `BRPOP <key> <timeout>`                          | Remove and retrieves the value from the right of the list. Or blocks for `<timeout>` seconds when the list is empty.  |
| `BLPOP <key> <timeout>`                          | Remove and retrieves the value from the left of the list. Or blocks for `<timeout>` seconds when the list is empty. |
| `BRPOPLPUSH <source-key> <dest-key> <timeout>`   | Removes the rightmost value from `<source-key>`; push the value to the left of `<dest-key>`; and retrieves the value. Or blocks for `<timeout>` seconds when `<source-key>` is empty. |

## Redis Hash Commands

|     Command                                           |  Description         |
|-------------------------------------------------------|----------------------|
| `HGET <key> <field>`                                  | Gets the hash field value. |
| `HSET <key> <field> <value>`                          | Sets the hash field value.  |
| `HSETNX <key> <field> <value>`                        | Sets the hash field value only if the field does not exist.  |
| `HMGET <key> <field> [<field> ...]`                   | Gets one or more hash field values. |
| `HMSET <key> <field> <value> [<field> <value> ...]`   | Sets one or more hash field values. |
| `HDEL <key> <field>`                                  | Removes the hash field. |
| `HLEN <key>`                                          | Gets the number of fields in a hash. |
| `HEXISTS <key> <field>`                               | Determines whether a hash field exists or not. |
| `HKEYS <key>`                                         | Gets all the field names in a hash. |
| `HVALS <key>`                                         | Gets all the values in a hash. |
| `HGETALL <key>`                                       | Gets all the field-value pairs in a hash. |
| `HINCRBY <key> <field> <amount>`                      | Increments the integer value of a hash field by the given amount. |
| `HINCRBYFLOAT <key> <field> <amount>`                 | Increments the float value of a hash field by the given amount. |

## Redis Set Commands

- Commands on a single set

|     Command                                      |  Description         |
|--------------------------------------------------|----------------------|
| `SADD <key> <item> [<item> ...]`                 | Adds an element to a set. |
| `SREM <key> <item> [<item> ...]`                 | Removes an element from a set. |
| `SISMEMBER <key> <item>`                         | Determines whether an item is in a set. |
| `SCARD <key> <item>`                             | Gets the cardinality in a set. |
| `SMEMBERS <key>`                                 | Gets all the members in a set. |
| `SRANDMEMBER <key> [<count>]`                    | Get 1 or `<count>` random members. If `<count> gt 0`, the members are unique. If `<count> lt 0`, the members may repeat.
| `SPOP <key>`                                     | Randomly removes an element from a set; and returns the element. |
| `SMOVE <source-key> <dest-key> <item>`           | If `<source-key>` has the element, removes the element from `<source-key>` and adds it to `<dest-key>`. Returns 1 on success, 0 otherwise.|

- Commands on multiple sets

|     Command                                      |  Description         |
|--------------------------------------------------|----------------------|
| `SDIFF <key1> [<key2> ...]`                      | Returns the items in  the first set `<key1>` but not in other sets. |
| `SINTER <key1> [<key2> ...]`                     | Returns the items in all sets. |
| `SUNION <key1> [<key2> ...]`                     | Returns the items in any sets. |
| `SDIFFSTORE <dest-key> <key1> [<key2> ...]`      | Adds in the `<dest-key>` the items in the first set `<key1>` but not in other sets. |
| `SINTERSTORE <dest-key> <key1> [<key2> ...]`     | Adds in the `<dest-key>` the items in all sets. |
| `SUNIONSTORE <dest-key> <key1> [<key2> ...]`     | Adds in the `<dest-key>` the items in any sets.  |

## Redis Ordered Set Commands

|     Command             |  Description         |
|-------------------------|----------------------|

## Redis HyperLogLog Commands

|     Command                 |  Description         |
|-----------------------------|----------------------|
| `PFADD <key> <element>`           |  Adds the specified elements to the specified HyperLogLog.  |
| `PPFCOUNT <key>`                | Returns the approximated cardinality of the set(s) observed by the HyperLogLog.  |
| `PPFMERGE <destkey> <key> [<key> ...]`  | Merges different HyperLogLogs into a single one.   |

## Redis Geo Commands

|     Command             |  Description         |
|-------------------------|----------------------|