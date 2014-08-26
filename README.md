gawkRedis
=========

A [GAWK](https://www.gnu.org/software/gawk/) (the GNU implementation of the AWK Programming Language) client library for Redis.

The gawkRedis is an extension library that enables gawk (GNU awk), to process data from a [Redis](http://redis.io/) datastore.

This project should become part of [gawkextlib](http://sourceforge.net/projects/gawkextlib) and provides an API for communicating with the [Redis](http://redis.io/) key-value store, using [hiredis](https://github.com/redis/hiredis)  (C client for Redis).


You can send comments, patches, questions [here on github](https://github.com/paulinohuerta/gawkRedis/issues) or to .... ([@paulinohuerta](http://twitter.com/paulinohuerta)).

# Table of contents
-----
1. [Installing/Configuring](#installingconfiguring)
   * [Installation](#installation)
1. [Functions](#functions)
   * [Connection](#connection)
   * [Keys and strings](#keys-and-strings)
   * [Hashes](#hashes)
   * [Lists](#lists)
   * [Sets](#sets)
   * [Sorted sets](#sorted-sets)
   * [Pub/sub](#pubsub)
-----

# Installing/Configuring
-----

Everything you should need to install gawkRedis on your system.

## Installation

* Install [hiredis](https://github.com/redis/hiredis), library C client for Redis.

* Install [gawkextlib](http://sourceforge.net/projects/gawkextlib), and *before configuration* of this project, you must replace two files and copy new one.
Try:

 Save the actual `config.am` file and you `replace` it with the file from our project of the same name.
  
 Then, you must proceed similarly with the `extension/Makefile.am` file.

 These two replacements are because as stated, this project is not yet integrated into the gawkextlib project and we must do this step manually.

 In any case, what we do is just prepare gawkextlib for the configuration process to manage redis.c (our C extension) as an extension of gawk.

 Finally, copies the file `redis.c` adding to the `extension folder`.

 Continue with:

 *Configuration and installation* gawkextlib as described in the project itself.

 And to finish the installation, you can check that was created `/path-to-gawk/lib/gawk/redis.so`, in this case you could try the following script myscript.awk:
~~~
@load "redis"
BEGIN{
  c=connectRedis() # the connection with the server: 127.0.0.1:6379
  if(c==-1) {
    print ERRNO # always you can to use the ERRNO variable for checking
  }
  ret=select(c,4) # the select redis command
  print "select devuelve "ret
  pong=ping(c) # the ping redis command
  print "The server says: "pong
  print echo(c,"foobared") # the echo redis command
  closeRedis(c)
}
~~~
which must run with:
~~~
/path-to-gawk/gawk -f myscript.awk /dev/null
~~~

# Functions
-----

## Connection

1. [connectRedis, connect](#connectRedis-connect) - Connect to a Redis server
1. [auth](#auth) - Authenticate to the server
1. [select](#select) - Change the selected database for the current connection
1. [closeRedis, disconnect](#closeRedis-disconnect) - Close the connection
1. [ping](#ping) - Ping the server
1. [echo](#echo) - Echo the given string

### connectRedis, connect
-----
_**Description**_: Connects to a Redis instance.

##### *Parameters*

*host*: string, optional  
*port*: number, optional  

##### *Return value*
*connection handle*: number, `-1` on error.

##### *Example*
~~~
c=connectRedis('127.0.0.1', 6379);
c=connectRedis('127.0.0.1'); // port 6379 by default
c=connectRedis(); // host address 127.0.0.1 and port 6379 by default
~~~

### auth
-----
_**Description**_: Authenticate the connection using a password.

##### *Parameters*
*number*: connection  
*string*: password

##### *Return value*
`1` if the connection is authenticated, `null string` (empty string) otherwise.

##### *Example*
~~~
ret=auth(c,"fooXX");
if(ret) {
  # authenticated
}
else {
  # not authenticated
}
~~~

### select
-----
_**Description**_: Change the selected database for the current connection.

##### *Parameters*
*number*: dbindex, the database number to switch to

##### *Return value*
`1` in case of success, `-1` in case of failure.
##### *Example*
select(c,5)

### closeRedis, disconnect
-----
_**Description**_: Disconnects from the Redis instance.

##### *Parameters*
*number*: connection handle  

##### *Return value*
`1` on success, `-1` on error.

##### *Example*
~~~
ret=closeRedis(c)
if(ret==-1) {
  print ERRNO
}
~~~

### ping
-----
_**Description**_: Check the current connection status

##### *Parameters*
*number*: connection handle  

##### *Return value*
*string*: `PONG` on success.


### echo
-----
_**Description**_: Sends a string to Redis, which replies with the same string

##### *Parameters*
*number*: connection
*string*: The message to send.

##### *Return value*
*string*: the same message.


## Keys and Strings

### Strings
-----

* [append](#append) - Append a value to a key
* [bitcount](#bitcount) - Count set bits in a string
* [bitop](#bitop) - Perform bitwise operations between strings
* [decr, decrby](#decr-decrby) - Decrement the value of a key
* [get](#get) - Get the value of a key
* [getbit](#getbit) - Returns the bit value at offset in the string value stored at key
* [getrange](#getrange) - Get a substring of the string stored at a key
* [getset](#getset) - Set the string value of a key and return its old value
* [incr, incrby](#incr-incrby) - Increment the value of a key
* [incrbyfloat](#incrbyfloat) - Increment the float value of a key by the given amount
* [mget](#mget) - Get the values of all the given keys
* [mset](#mset) - Set multiple keys to multiple values
* [set](#set) - Set the string value of a key
* [setbit](#setbit) - Sets or clears the bit at offset in the string value stored at key
* [setrange](#setrange) - Overwrite part of a string at key starting at the specified offset
* [strlen](#strlen) - Get the length of the value stored in a key

### Keys
-----

* [del](#del) - Delete a key
* [dump](#dump) - Return a serialized version of the value stored at the specified key.
* [exists](#exists) - Determine if a key exists
* [expire, pexpire](#expire-pexpire) - Set a key's time to live in seconds
* [keys](#keys) - Find all keys matching the given pattern
* [move](#move) - Move a key to another database
* [persist](#persist) - Remove the expiration from a key
* [randomkey](#randomkey) - Return a random key from the keyspace
* [rename](#rename) - Rename a key
* [renamenx](#renamenx) - Rename a key, only if the new key does not exist
* [type](#type) - Determine the type stored at key
* [sort](#sort) - Sort the elements in a list, set or sorted set
* [ttl, pttl](#ttl-pttl) - Get the time to live for a key
* [restore](#restore) - Create a key using the provided serialized value, previously obtained with [dump](#dump).

-----

### get
-----
_**Description**_: Get the value related to the specified key

##### *Parameters*
*number*: connection  
*string*: the key

##### *Return value*
*string*: `key value` or `null string` (empty string) if key didn't exist.

##### *Examples*
~~~
value=get(c,"key1")
~~~

### set
-----
_**Description**_: Set the string value in argument as value of the key.  If you're using Redis >= 2.6.12, you can pass extended options as explained below

##### *Parameters*
*number*: connection  
*string*: key  
*string*: value  
*and optionally*: "EX",timeout,"NX" or "EX",timeout,"XX" or "PX" instead of "EX"

##### *Return value*
`1` if the command is successful `string null` if no success, or `-1` on error.

##### *Examples*
~~~
# Simple key -> value set
set(c,"key","value");

# Will redirect, and actually make an SETEX call
set(c,"mykey1","myvalue1","EX",10)

# Will set the key, if it doesn't exist, with a ttl of 10 seconds
set(c,"mykey1","myvalue1","EX",10,"NX")

# Will set a key, if it does exist, with a ttl of 10000 miliseconds

set(c,"mykey1","myvalue1","PX",10000,"XX")
~~~

### del
-----
_**Description**_: Remove specified keys.

##### *Parameters*
*string or array of string*: `key name` or `array name` containing the names of the keys

##### *Return value*
*number*: Number of keys deleted.

##### *Examples*
~~~
set(c,"keyX","valX")
set(c,"keyY","valY")
set(c,"keyZ","valZ")
set(c,"keyU","valU")
AR[1]="keyY"
AR[2]="keyZ"
AR[3]="keyU"
del(c,"keyX") # return 1 
del(c,AR) # return 3
~~~


### exists
-----
_**Description**_: Verify if the specified key exists.

##### *Parameters*
*number*: connection  
*string*: key name

##### *Return value*
`1` If the key exists, `0` if the key no exists.

##### *Examples*
~~~
set(c,"key","value");
exists(c,"key"); # return 1
exists(c,"NonExistingKey") # return 0
~~~

### incr, incrby
-----
_**Description**_: Increment the number stored at key by one. If the second argument is filled, it will be used as the integer value of the increment.

##### *Parameters*
*number*: connection  
*string*: key name  
*number*: value that will be added to key (only for incrby)

##### *Return value*
*number*: the new value

##### *Examples*
~~~
incr(c,"key1") # key1 didn't exists, set to 0 before the increment
               # and now has the value 1
incr(c,"key1") #  value 2
incr(c,"key1") #  value 3
incr(c,"key1") #  value 4
incrby(c,"key1",10) #  value 14
~~~

### incrbyfloat
-----
_**Description**_: Increment the key with floating point precision.

##### *Parameters*
*number*: connection  
*string*: key name  
*value*: (float) value that will be added to the key  

##### *Return value*
*number*: the new value

##### *Examples*
~~~
incrbyfloat(c,"key1", 1.5)  # key1 didn't exist, so it will now be 1.5
incrbyfloat(c,"key1", 1.5)  # 3
incrbyfloat(c,"key1", -1.5) # 1.5
incrbyfloat(c,"key1", 2.5)  # 4
~~~

### decr, decrby
-----
_**Description**_: Decrement the number stored at key by one. If the second argument is filled, it will be used as the integer value of the decrement.

##### *Parameters*
*number*: connection  
*string*: key name  
*number*: value that will be substracted to key (only for decrby)

##### *Return value*
*number*: the new value

##### *Examples*
~~~
decr(c,"keyXY") # keyXY didn't exists, set to 0 before the increment 
                # and now has the value -1
decr(c,"keyXY")   # -2
decr(c,"keyXY")   # -3
decrby(c,"keyXY",10)  # -13
~~~

### mget
-----
_**Description**_: Get the values of all the specified keys. If one or more keys dont exist, the array will contain `null string` at the position of the key.

##### *Parameters*
*number*: connection  
*Array*: Array containing the list of the keys  
*Array*: Array of results, containing the values related to keys in argument

##### *Return value*
`1` success `-1` on error

##### *Examples*

~~~
@load "redis"
BEGIN{
 null="\"\""
 c=connectRedis()
 set(c,"keyA","val1")
 set(c,"keyB","val2")
 set(c,"keyC","val3")
 set(c,"keyD","val4")
 set(c,"keyE","")
 AR[1]="keyA"
 AR[2]="keyB"
 AR[3]="keyZ" # this key no exists
 AR[4]="keyC"
 AR[5]="keyD"
 AR[6]="keyE"
 ret=mget(c,AR,K) # K is the array with results
 for(i in K) {
   if(!K[i]) {
     if(exists(c,AR[i])){ # function exists was described previously
       print i": "AR[i]" ----> "null
     }
     else {
       print i": "AR[i]" ----> not exists"
     }
   }
   else {
     print i": "AR[i]" ----> ""\""K[i]"\""
   }
 }
 closeRedis(c)
}
~~~


### getset
-----
_**Description**_: Sets a value and returns the previous entry at that key.
##### *Parameters*
*number*: connection  
*string*: key name   
*string*: key value

##### *Return value*
A string, the previous value located at this key

##### *Example*
~~~
set(c,"x", "42")
exValue=getset(c,"x","lol") # return "42", now the value of x is "lol"
newValue = get(c,"x") # return "lol"
~~~

### randomKey
-----
_**Description**_: Returns a random key.

##### *Parameters*
*number*: connection  

##### *Return value*
*string*: a random key from the currently selected database

##### *Example*
~~~
print randomkey(c)
~~~

### move
-----
_**Description**_: Moves a key to a different database. The key will move only if not exists in destination database.

##### *Parameters*
*number*: connection  
*string*: key, the key to move  
*number*: dbindex, the database number to move the key to  

##### *Return value*
`1` if key was moved, `0` if key was not moved.

##### *Example*
~~~
select(c,0)	# switch to DB 0
set(c,"x","42") # write 42 to x
move(c,"x", 1)	# move to DB 1
select(c,1);	# switch to DB 1
get(c,"x");	# will return 42
~~~

### rename
-----
_**Description**_: Renames a key. If newkey already exists it is overwritten.

##### *Parameters*
*number*: connection  
*string*: srckey, the key to rename.  
*string*: dstkey, the new name for the key.

##### *Return value*
`1` in case of success, `-1` in case of error.

##### *Example*
~~~
set(c,"x", "valx");
rename(c,"x","y");
get(c,"y")  # return "valx"
get(c,"x")  # return null string, because x no longer exists
~~~

### renamenx
-----
_**Description**_: Same as rename, but will not replace a key if the destination already exists. This is the same behaviour as set and option nx.

##### *Return value*
`1` in case of success, `0` in case not success.

### expire, pexpire
-----
_**Description**_: Sets an expiration date (a timeout) on an item. pexpire requires a TTL in milliseconds.

##### *Parameters*
*number*: connection  
*string*: key name. The key that will disappear.  
*number*: ttl. The key's remaining Time To Live, in seconds.

##### *Return value*
`1` in case of success, `0` if key does not exist or the timeout could not be set
##### *Example*
~~~
ret=set(c,"x", "42")  # ret value 1; x value "42"
expire(c,"x", 3)      # x will disappear in 3 seconds.
system("sleep 5")     # wait 5 seconds
get(c,"x")  # will return null string, as x has expired.
~~~

### keys
-----
_**Description**_: Returns the keys that match a certain pattern. Check supported [glob-style patterns](http://redis.io/commands/keys)

##### *Parameters*
*number*: connection  
*string*: pattern  
*array of strings*: the results, the keys that match a certain pattern.

##### *Return value*
`1` in case of success, `-1` on error

##### *Example*
~~~
keys(c,"*",AR)    # all keys will match this.
# show AR contains
delete AR
keys(c,"user*",AR)  # for matching all keys begining with "user"
for(i in AR) {
  print i": "AR[i]
}
~~~

### type
-----
_**Description**_: Returns the type of data pointed by a given key.

##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
*string*: the type of the data (string, list, set, zset and hash) or `none` when the key does not exist.

##### *Example*
~~~
set(c,"keyZ","valZ")
ret=type(c,"keyZ") # ret contains "string"
# showing the "type" all keys of DB 4
select(c,4)
keys(c,"*",KEYS) 
for(i in KEYS){
  print i": "KEYS[i]" ---> "type(c,KEYS[i])
} 
~~~

### append
-----
_**Description**_: Append specified string to the string stored in specified key.

##### *Parameters*
*number*: connection  
*string*: key name   
*string*: value

##### *Return value*
*number*: Size of the value after the append

##### *Example*
~~~
set(c,"key","value1")
append(c,"key","value2")   # 12 
get(c,"key")   # "value1value2"
~~~

### getrange
-----
_**Description**_: Return a substring of a larger string 

##### *Parameters*
*number*: connection  
*string*: key name  
*number*: start  
*number*: end  

##### *Return value*
*string*: the substring 

##### *Example*
~~~
set(c,"key","string value");
print getrange(c,"key", 0, 5)  # "string"
print getrange(c,"key", -5, -1)  # "value"
~~~

### setrange
-----
_**Description**_: Changes a substring of a larger string.

##### *Parameters*
*number*: connection  
*string*: key name  
*number*: offset  
*string*: value

##### *Return value*
*string*: the length of the string after it was modified.

##### *Example*
~~~
set(c,"key1","Hello world")
ret=setrange(c,"key1",6,"redis") # ret value 11
get(c,"key1") # "Hello redis"
~~~

### strlen
-----
_**Description**_: Get the length of a string value.

##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
*number*

##### *Example*
~~~
set(c,"key","value")
strlen(c,"key")  # 5
~~~

### getbit
-----
_**Description**_: Return a single bit out of a larger string

##### *Parameters*
*number*: connection  
*string*: key name  
*number*: offset

##### *Return value*
*number*: the bit value (0 or 1)

##### *Example*
~~~
set(c,"key", "\x7f"); // this is 0111 1111
getbit(c,"key", 0) # 0
getbit(c,"key", 1) # 1
set(c,"key", "s"); // this is 0111 0011
print getbit(c,"key", 5) # 0
print getbit(c,"key", 6) # 1
print getbit(c,"key", 7) # 1
~~~

### setBit
-----
_**Description**_: Changes a single bit of a string.

##### *Parameters*
*number*: connection  
*string*: key name   
*number*: offset  
*number*: value (1 or 0)

##### *Return value*
*number*: 0 or 1, the value of the bit before it was set.

##### *Example*
~~~
@load "redis"
BEGIN{
  c=connectRedis()
  set(c,"key", "*") # ord("*") = 42 = "0010 1010"
  setbit(c,"key", 5, 1) #  returns 0
  setbit(c,"key", 7, 1) # returns 0
  print get(c,"key") #  "/" = "0010 1111"
  set(c,"key1","?") # 00111111
  print get(c,"key1")
  print "key1: changing bit 7, it returns "setbit(c,"key1", 7, 0) # returns 1
  print "key1: value actual is 00111110"
  print get(c,"key1") # retorna ">"
  closeRedis(c)
}
~~~

### bitop
-----
_**Description**_: Bitwise operation on multiple keys.

##### *Parameters*
*number*: connection  
*operator*: either "AND", "OR", "NOT", "XOR"   
*ret_key*: result key   
*array or string*: array containing the keys or only one string (in case of using the NOT operator).

##### *Return value*
*number*: The size of the string stored in the destination key.

### bitcount
-----
_**Description**_: Count bits in a string.

##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
*number*: The number of bits set to 1 in the value behind the input key.

### sort
-----
_**Description**_: Sort the elements in a list, set or sorted set.

##### *Parameters*
*number*: connection  
*string*: key name  
*array*: the array with the result  
*string*: options "desc|asc alpha"

##### *Return value*
`1` or `-1`on error 

##### *Example*
~~~
c=connectRedis()
del(c,"thelist1");
print type(c,"thelist1") # none
lpush(c,"thelist1","bed")
lpush(c,"thelist1","pet")
lpush(c,"thelist1","key")
lpush(c,"thelist1","art")
lrange(c,"thelist1",AR,0, -1)
for(i in AR){
  print i") "AR[i]
}
delete AR
# sort desc "thelist1"
ret=sort(c,"thelist1",AR,"alpha desc")
print "-----"
for(i in AR){
  print i") "AR[i]
}
print "-----"
~~~

### ttl, pttl
-----
_**Description**_: Returns the time to live left for a given key in seconds (ttl), or milliseconds (pttl).

##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
*number*:  The time to live in seconds.  If the key has no ttl, `-1` will be returned, and `-2` if the key doesn't exist.

##### *Example*
~~~
ttl(c,"key")
~~~

### persist
-----
_**Description**_: Remove the expiration timer from a key.

##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
`1` if a timeout was removed, `0` if key does not exist or does not have an associated timeout

##### *Example*
~~~
exists(c,"key)    # return 1
ttl(c,"key")      # returns -1 if has no associated expire
expire(c,"key",100)  # returns 1
persist(c,"key")     # returns 1
persist(c,"key")     # returns 0
~~~

### mset, msetnx
-----
_**Description**_: Sets multiple key-value pairs in one atomic command. msetnx only returns `1` if all the keys were set (see set and option nx).

##### *Parameters*
*number*: connection  
*array*: keys and their respectives values  

##### *Return value*
`1` in case of success, `-1` on error. while msetnx returns `0` if no key was set (at least one key already existed).

##### *Example*
~~~
@load "redis"
BEGIN {
 AR[1]="q1"
 AR[2]="vq1"
 AR[3]="q2"
 AR[4]="vq2"
 AR[5]="q3"
 AR[6]="vq3"
 AR[7]="q4"
 AR[8]="vq4"
 c=connect()
 ret=mset(c,AR)
 print ret" returned by mset"
 keys(c,"q*",R)
 for(i in R){
   print i") "R[i]
 }
 closeRedis(c)
}
~~~

Output:
~~~
1 returned by mset
1) q2
2) q3
3) q4
4) q1
~~~

### dump
-----
_**Description**_: Dump a key out of a redis database, the value of which can later be passed into redis using the RESTORE command.  The data that comes out of DUMP is a binary representation of the key as Redis stores it.

##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
The Redis encoded value of the key, or `string null` if the key doesn't exist

##### *Examples*
~~~
set(c,"foo","bar")
val=dump(c,"foo")  # val will be the Redis encoded key value
~~~

### restore
-----
_**Description**_: Restore a key from the result of a DUMP operation.
##### *Parameters*
*number*: connection  
*string*: key name.  
*number*: ttl number. How long the key should live (if zero, no expire will be set on the key).  
*string*: value string (binary).  The Redis encoded key value (from DUMP).

##### *Return value*
`1` on sucess, `-1` on error

##### *Examples*
~~~
set(c,"foo","bar")
val=dump(c,"foo")
restore(c,"bar",0,val)  # The key "bar", will now be equal to the key "foo"
~~~

## Hashes

* [hdel](#hdel) - Delete one or more hash fields
* [hexists](#hexists) - Determine if a hash field exists
* [hget](#hget) - Get the value of a hash field
* [hgetAll](#hgetall) - Get all the fields and values in a hash
* [hincrby](#hincrby) - Increment the integer value of a hash field by the given number
* [hincrbyfloat](#hincrbyfloat) - Increment the float value of a hash field by the given amount
* [hkeys](#hkeys) - Get all the fields in a hash
* [hlen](#hlen) - Get the number of fields in a hash
* [hmget](#hmget) - Get the values of all the given hash fields
* [hmset](#hmset) - Set multiple hash fields to multiple values
* [hset](#hset) - Set the string value of a hash field
* [hsetnx](#hsetnx) - Set the value of a hash field, only if the field does not exist
* [hvals](#hvals) - Get all the values in a hash

### hset
-----
_**Description**_: Adds a value to the hash stored at key. If this value is already in the hash, `FALSE` is returned.  
##### *Parameters*
*number*: connection  
*string*: key name.  
*string*: hash Key   
*string*: value  

##### *Return value*
`1` if value didn't exist and was added successfully, `0` if the value was already present and was replaced, `-1` if there was an error.
##### *Example*
~~~
@load "redis"
BEGIN{
 c=connectRedis()
 del(c,"thehash")
 hset(c,"thehash","key1","hello") # returns 1
 hget(c,"thehash", "key1") # returns "hello"
 hset(c,"thehash", "key1", "plop") # returns 0, value was replaced
 hget(c,"thehash", "key1") # returns "plop"
 closeRedis(c)
}
~~~

### hsetnx
-----
_**Description**_: Adds a value to the hash stored at key only if this field isn't already in the hash.

##### *Return value*
`1` if the field was set, `0` if it was already present.

##### *Example*
~~~
del(c,"thehash")
hsetnx(c,"thehash","key1","hello") # returns 1
hget(c,"thehash", "key1") # returns "hello"
hsetnx(c,"thehash", "key1", "plop") # returns 0. No change, value wasn't replaced
hget(c,"thehash", "key1") # returns "hello"
~~~

### hget
-----
_**Description**_: Gets a value associated with a field from the hash stored it key.

##### *Parameters*
*number*: connection  
*string*: key name  
*string*: hash field  

##### *Return value*
*string*: the value associated with field, or `string null` when field is not present in the hash or the key does not exist.


### hlen
-----
_**Description**_: Returns the length of a hash, in number of items
##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
*number*: the number of fields in the hash, or `0` when key does not exist. `-1` on error (by example if key exist and isn't a hash).

##### *Example*
~~~
hsetnx(c,"thehash","key1","hello1") # returns 1
hsetnx(c,"thehash","key2","hello2") # returns 1
hsetnx(c,"thehash","key3","hello3") # returns 1
hlen(c,"thehash")  # returns 3
~~~

### hdel
-----
_**Description**_: Removes the specified fields from the hash stored at key.

##### *Parameters*
*number*: connection  
*string*: key name  
*string or array*: field name, or array name containing the field names 

##### *Return value*
*number*: the number of fields that were removed from the hash, not including specified but non existing fields.


### hkeys
-----
_**Description**_: Obtains the keys in a hash.

##### *Parameters*
*number*: connection  
*Key*: key name  
*array*: containing field names results

##### *Return value*
`1` on success, `0` if the hash is empty or no exists

##### *Example*
~~~
@load "redis"
BEGIN{
 c=connectRedis()
 hkeys(c,"thehash",A) # returns 1
 for(i in A){
   print i": "A[i]
 }
 closeRedis(c)
}
~~~
The order is random and corresponds to redis' own internal representation of the structure.

### hvals
-----
_**Description**_: Obtains the values in a hash.

##### *Parameters*
*number*: connection  
*Key*: key name  
*array*: contains the result with values

##### *Return value*
`1` on success, `0` if the hash is empty or no exists

##### *Example*
~~~
@load "redis"
BEGIN{
 c=connectRedis()
 hvals(c,"thehash",A) # returns 1
 for(i in A){
   print i": "A[i]
 }
 closeRedis(c)
}
~~~

The order is random and corresponds to redis' own internal representation of the structure.

### hgetall
-----
_**Description**_: Returns the whole hash.

##### *Parameters*
*number*: connection  
*Key*: key name  
*array*: for the result, contains the entire sequence of field/value

##### *Return value*
`1` on success, `0` if the hash is empty or no exists

##### *Example*
~~~
@load "redis"
BEGIN{
 c=connectRedis()
 hgetall(c,"thehash",A) # returns 1
 n=length(A)
 for(i=1;i<=n;i+=2){
   print i": "A[i]" ---> "A[i+1]
 }
 closeRedis(c)
}
~~~

The order is random and corresponds to redis' own internal representation of the structure.


### hexists
-----
_**Description**_: Verify if the specified member exists in a hash.
##### *Parameters*
*number*: connection  
*string*: key name  
*string*: field or member  

##### *Return value*
`1` If the member exists in the hash, otherwise return `0`.

##### *Examples*
~~~


~~~

### hincrby
-----
_**Description**_: Increments the value of a member from a hash by a given amount.

##### *Parameters*
*number*: connection  
*string*: key name  
*string*: member or field    
*number*: (integer) value that will be added to the member's value  

##### *Return value*
*number*: the new value

##### *Examples*
~~~

~~~

### hincrbyfloat
-----
_**Description**_: Increments the value of a hash member by the provided float value

##### *Parameters*
*number*:connection  
*string*: key name   
*string*: field name   
*number*: (float) value that will be added to the member's value  

##### *Return value*
*number*: the new value

##### *Examples*
~~~
del(c,"h");
hincrbyfloat(c,"h","x",1.5);  # returns 1.5: field x = 1.5 now
hincrbyfloat(c,"h","x", 1.5)  # returns 3.0: field x = 3.0 now
hincrbyfloat(c,"h","x",-3.0)  # returns 0.0: field x = 0.0 now
~~~

### hmset
-----
_**Description**_: Fills in a whole hash. Overwriting any existing fields in the hash. If key does not exist, a new key holding a hash is created.

##### *Parameters*
*number* connection  
*string*: key name  
*array*: contains field names and their respective values

##### *Return value*
`1` on success, `-1` on error

##### *Examples*
~~~
c=connection()
AR[1]="a0"
AR[2]="value of a0"
AR[3]="a1"
AR[4]="value of a1"
ret=hmget(c,"hash1",AR1)
~~~

### hmget
-----
_**Description**_: Retrieve the values associated to the specified fields in the hash.

##### *Parameters*
*number*: connection  
*string: key name  
*array* contains field names  
*array* contains results, a sequence of values associated with the given fields, in the same order as they are requested. For every field that does not exist in the hash, a null string (empty string) is associated.

##### *Return value*
`1` on success, `-1` on error

##### *Examples*
~~~
@load "redis"
BEGIN{
 c=connect()
 J[1]="c2"
 J[2]="k3"
 J[3]="cl1"
 J[4]="c1"
 J[5]="c6"
 ret=hmget(c,"thash",J,T)
 if(ret==-1) {
   print ERRNO
 }
 print "hmget: Results and requests"
 for (i in T) {
   print i": ",T[i], " ........ ",J[i]
 }
 ret=hgetall(c,"thash",AR)
 print "hgetall from the hash thash"
 for (i in AR) {
   print i": "AR[i]
 }
 # other use allowed for hmget
 ret=hmget(c,"thash","cl1",OTH)
 print "is cl1 a field?"
 for(i in OTH){
   print i": "OTH[i]
 }
 closeRedis(c);
 print ERRNO
}
~~~
Output:
~~~
hmget: Results and requests
1:    ........  c2
2:  vk3  ........  k3
3:  vcl1  ........  cl1
4:    ........  c1
5:    ........  c6
hgetall from the hash thash
1: k1
2: vk1
3: k3
4: vk3
5: cl1
6: vcl1
7: cl2
8: vcl2
is cl1 a field?
1: vcl1
~~~


## Lists

* [llen](#llen) - Get the length/size of a list
* [lpop](#lpop) - Remove and get the first element in a list
* [lpush](#lpush) - Prepend one or multiple values to a list
* [lrange](#lrange) - Get a range of elements from a list
* [lrem](#lrem) - Remove elements from a list
* [lset](#lset) - Set the value of an element in a list by its index
* [ltrim](#ltrim) - Trim a list to the specified range
* [rpop](#rpop) - Remove and get the last element in a list


### lpop
-----
_**Description**_: Return and remove the first element of the list.

##### *Parameters*
*number*: connection  
*string* key name  

##### *Return value*
*string*: the value, `null string` in case of empty list or no exists

##### *Example*
~~~
@load "redis"
BEGIN{
 c=connectRedis()
 ret=del(c,"list1")
 print "return del="ret
 ret=lpush(c,"list1","AA")
 print "return lpush="ret
 ret=lpush(c,"list1","BB")
 print "return lpush="ret
 ret=lpush(c,"list1","CC")
 print "return lpush="ret
 ret=lrange(c,"list1",AR,0,-1)
 print "return lrange="ret
 for(i in AR) {
   print i": "AR[i]
 }
 ret=lpop(c,"list1")
 print "return lpop="ret
 delete AR
 ret=lrange(c,"list1",AR,0,-1)
 print "return lrange="ret
 for(i in AR) {
   print i": "AR[i]
 }
 closeRedis(c)
}
~~~
Output:
~~~
return del=1
return lpush=1
return lpush=2
return lpush=3
return lrange=1
1: CC
2: BB
3: AA
return lpop=CC
return lrange=1
1: BB
2: AA
~~~

### lpush
-----
_**Description**_: Adds the string value to the head (left) of the list. Creates the list if the key didn't exist. If the key exists and is not a list, `-1` is returned.

##### *Parameters*
*number*: connection  
*key*: key name  
*string*: the string value to push in key

##### *Return value*
*number* The new length of the list in case of success, `-1` in case of Failure.

##### *Examples*
~~~
lpush(c,"list1","dd")
~~~

### lrange
-----
_**Description**_: Returns the specified elements of the list stored at the specified key in the range [start, end]. start and stop are interpretated as indices:
0 the first element, 1 the second ...
-1 the last element, -2 the penultimate ...

##### *Parameters*
*number*: connection  
*string*: key name  
*array*: for the result. It will contain the values in specified range  
*number*: start   
*number*: end  

##### *Return value*
`1` on success, `0` in case of empty list or no exists

##### *Example*
~~~
lrange(c,"list1",AR,0,-1)  # it range includes all values.
~~~

### lrem
-----
_**Description**_: Removes the first `count` occurences of the value element from the list. If count is zero, all the matching elements are removed. If count is negative, elements are removed from tail to head.

##### *Parameters*
*number*: connection  
*string*: key name  
*number*: count  
*string*: value   

##### *Return value*
*number* the number of elements to remove, `-1` on error

##### *Example*
~~~
@load "redis"
BEGIN{
  c=connectRedis()
  del(c,"list1")
  lpush(c,"list1","AA")
  lpush(c,"list1","BB")
  lpush(c,"list1","CC")
  lpush(c,"list1","BB")
  lrange(c,"list1",AR,0,-1)
  for(i in AR) {
    print i": "AR[i]
  }
  ret=lrem(c,"list1",4,"BB") # count is 4 but removes only two (existing values)
  print "return lrem="ret
  if(ret==-1) print ERRNO
  delete AR
  ret=lrange(c,"list1",AR,0,-1)
  for(i in AR) {
     print i": "AR[i]
  }
  closeRedis(c)
}

### lset
-----
_**Description**_: Set the list at index with the new value.

##### *Parameters*
*number*: connection  
*string*: key name   
*number*: index  
*string*: value  

##### *Return value*
`1` if the new value is setted. `-1` on error (if the index is out of range, or data type identified by key is not a list).

##### *Example*
~~~

~~~

### ltrim
-----
_**Description**_: Trims an existing list so that it will contain only a specified range of elements.

##### *Parameters*
*number*: connection  
*string*: key name  
*number*: start  
*number*: stop  

##### *Return value*
`1` on success, `-1` on error.

##### *Example*
~~~

~~~

### rpush
-----
_**Description**_: Adds the string value to the tail (right) of the list. Creates the list if the key didn't exist. If the key exists and is not a list, `FALSE` is returned.

##### *Parameters*
*number*: connection  
*string*: key name    
*string*: the string value to push in key

##### *Return value*
*number* The new length of the list in case of success, `-1` on error.

##### *Examples*
~~~

~~~

### llen
-----
_**Description**_: Returns the size of a list identified by Key.

If the list didn't exist or is empty, the command returns 0. If the data type identified by Key is not a list, the command return `FALSE`.

##### *Parameters*
*number*: connection  
*string*: key name  

##### *Return value*
*number*: the size of the list identified by Key exists, `0` if the key no exist, `-1` on error (if the data type identified by key is not list)

##### *Example*
~~~

~~~


## Sets

* [sAdd](#sadd) - Add one or more members to a set
* [sCard, sSize](#scard-ssize) - Get the number of members in a set
* [sDiff](#sdiff) - Subtract multiple sets
* [sDiffStore](#sdiffstore) - Subtract multiple sets and store the resulting set in a key
* [sInter](#sinter) - Intersect multiple sets
* [sInterStore](#sinterstore) - Intersect multiple sets and store the resulting set in a key
* [sIsMember, sContains](#sismember-scontains) - Determine if a given value is a member of a set
* [sMembers, sGetMembers](#smembers-sgetmembers) - Get all the members in a set
* [sMove](#smove) - Move a member from one set to another
* [sPop](#spop) - Remove and return a random member from a set
* [sRandMember](#srandmember) - Get one or multiple random members from a set
* [sRem, sRemove](#srem-sremove) - Remove one or more members from a set
* [sUnion](#sunion) - Add multiple sets
* [sUnionStore](#sunionstore) - Add multiple sets and store the resulting set in a key



## Sorted sets

* [zadd](#zadd) - Add one or more members to a sorted set or update its score if it already exists
* [zcard, zSize](#zcard-zsize) - Get the number of members in a sorted set
* [zcount](#zcount) - Count the members in a sorted set with scores within the given values
* [zincrby](#zincrby) - Increment the score of a member in a sorted set
* [zinterstore](#zinterstore) - Intersect multiple sorted sets and store the resulting sorted set in a new key
* [zrange](#zrange) - Return a range of members in a sorted set, by index
* [zrangeWithScore, zRevRangeByScore](#zrangebyscore-zrevrangebyscore) - Return a range of members in a sorted set, by score
* [zrank, zrevrank](#zrank-zrevrank) - Determine the index of a member in a sorted set
* [zrem](#zrem-zdelete) - Remove one or more members from a sorted set
* [zscan](#zscan) - 
* [zscore](#zscore) - Get the score associated with the given member in a sorted set
* [zunionstore](#zunionstore) - Add multiple sorted sets and store the resulting sorted set in a new key


## Pub/sub

* [publish](#publish) - Post a message to a channel
* [subscribe](#subscribe) - Subscribe to channels
* [getMessage](#getMessage) - 


### publish
-----
_**Description**_: Publish messages to channels.

##### *Parameters*
*number*: connection  
*string*: a channel to publish to  
*string*: a string messsage  

##### *Return value*
*number*: the number of clients that received the message

##### *Example*
~~~
publish(c,"chan-1", "hello, world!") # send message.
~~~

### subscribe
-----
_**Description**_: Subscribe to channels.

##### *Parameters*
*number*: connection  
*string or array*: the channel name or the array containing the names of channels  

##### *Return value*
`1` on success, `-1` on error

##### *Example*
~~~
subscribe(c,"chan-2")  # returns 1, subscribes to chan-1
#
CH[1]="chan-1"
CH[2]="chan-2"
CH[3]="chan-3"
#
subscribe(c,CH)  # returns 1, subscribes to chan-1, chan-2 and chan-3
~~~

### getMessage
-----
_**Description**_: 

##### *Parameters*
*number*: connection  
*array*: containing the messages received  

##### *Return value*
`1` on success, `-1` on error

##### *Example*
~~~
A[1]="c1"
A[2]="c2"
ret=subscribe(c,A)
while(ret=getMessage(c,B)) {
   for(i in B){
     print i") "B[i]
   }
   delete B
}
~~~
