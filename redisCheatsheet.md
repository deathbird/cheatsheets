# Redis

## Links
[Redis usage patterns](https://www.slideshare.net/itamarhaber/redis-use-patterns-devcontlv-june-2014)  
https://www.slideshare.net/carlosabalde/everything-you-always-wanted-to-know-about-redis-but-were-afraid-to-ask  
https://www.slideshare.net/noahd1/redis-in-practice  
https://www.slideshare.net/dvirsky/kicking-ass-with-redis  
https://www.slideshare.net/Byungwook/redis-data-modeling-examples  
https://www.slideshare.net/ryanbriones/the-beauty-of-simplicity-mastering-database-design-with-redis  

## Usage scenarios
when in no need for rdbms:
- carts
- sessions
- last viewed profiles (list)

## Various  
key names:
- use colons to separate parts of key names
- Your schema is your key's name so keep them in order

## redis-cli
```
$ docker exec -it <container_name> bash
$ redis-cli -h redis -p 6379

# connect to database no 0
redis-cli
127.0.0.1:6379>

# connect to database no 2
redis-cli -n 2
127.0.0.1:6379[1]>

# connect to database no 2 from redis-cli
127.0.0.1:6379>select 2
127.0.0.1:6379[2]>

# flush from container
> flushall

# show info about databases
127.0.0.1:6379> INFO keyspace
# Keyspace
db13:keys=1,expires=0,avg_ttl=0

# display all matching keys with a pattern like: 'some-word'*
KEYS <pattern with wildcards>

# learn the type of value of <key>
TYPE some_key
>set

# Redis supports 5 data types. You need to know what type of value that a key maps to, 
# as for each data type, the command to retrieve it is different.
# Here are the commands to retrieve key value:

# if value is of type string -> GET <key>
# if value is of type hash -> HGETALL <key>
# if value is of type lists -> lrange <key> <start> <end>
# if value is of type sets -> smembers <key>
# if value is of type sorted sets -> ZRANGEBYSCORE <key> <min> <max>

```
