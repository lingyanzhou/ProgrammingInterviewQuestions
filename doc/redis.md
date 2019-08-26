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
  
  - byte strings,
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

- Bitmap

  A subtype of String.

- Pub/Sub
- Hyperloglog

  HyperLogLog provides a very good approximation of the cardinality of a set even using a very small amount of memory around 12 kbytes per key with a standard error of 0.81%.

  *Note* : The maximum cardinality is approximately 2^64.

- Geo

[Redis - Data Types](http://www.tutorialspoint.com/redis/redis_data_types.htm)

## Redis Key Command

|     Command             |  Description    |
| DEL <key>               |  Deletes the key, if it exists.  |
| EXISTS <key>             | Checks whether the key exists or not. |
| MOVE key db              | Moves a key to another database. |
| RENAME <key> <newKey>    | Changes the key name.  |
| RENAMENX <key> <newKey>  | Renames the key, if the new key doesn't exist. |
| TYPE <key>               | Returns the data type of the value stored in the key. |

## Redis String Command

- byte string operations:

|     Command                |  Description    |
|----------------------------|----------------------|
| `DEL <key>`                |  Deletes the key, if it exists.  |
| `EXISTS <key>`             | Checks whether the key exists or not. |
| `MOVE <key> <db>`          | Moves a key to another database. |
| `RENAME <key> <newKey>`    | Changes the key name.  |
| `RENAMENX <key> <newKey>`  | Renames the key, if the new key doesn't exist. |
| `TYPE <key>`               | Returns the data type of the value stored in the key. |

- integer operations:

|     Command             |  Description    |
|-------------------------|----------------------|
| `INCR <key>`              | Increments the value by 1; returns the new value.  |
| `DECR <key>`              | Decrements the value by 1; returns the new value. |
| `INCRBY <key> <amount>`              | Increments the value by integer value `<amount>`; returns the new value. |
| `DECRBY <key> <amount>`              | Decrements the value by integer value `<amount>`; returns the new value. |
| `INCRBYFLOAT <key> <amount>`              |  Increments the value by float value `<amount>`; returns the new value.  |

- floating point operations:

|     Command             |  Description    |
|-------------------------|----------------------|

## Redis Hyperloglog Commands

Redis HyperLogLog Commands

|     Command                 |  Description         |
|-----------------------------|----------------------|
| PFADD key element           |  Adds the specified elements to the specified HyperLogLog.  |
| PPFCOUNT key                | Returns the approximated cardinality of the set(s) observed by the HyperLogLog at key(s).  |
| PPFMERGE destkey sourcekey  | Merges N different HyperLogLogs into a single one.   |