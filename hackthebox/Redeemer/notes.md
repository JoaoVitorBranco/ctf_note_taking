# Questions 

## Which TCP port is open on the machine?
- `nmap $IP -p- --min-rate 5000`

## Which service is running on the port that is open on the machine?
- `nmap $IP -p- --min-rate 5000`

## What type of database is Redis? Choose from the following options: (i) In-memory Database, (ii) Traditional Database
- In-memory Database

## Which command-line utility is used to interact with the Redis server? Enter the program name you would enter into the terminal without any arguments.
- `redis-cli`

## Which flag is used with the Redis command-line utility to specify the hostname?
- `-h`

## Once connected to a Redis server, which command is used to obtain the information and statistics about the Redis server?
- `info`

## What is the version of the Redis server being used on the target machine?
- 5.0.7

## Which command is used to select the desired database in Redis?
- `select`

## How many keys are present inside the database with index 0?
```bash
$IP:6379> SELECT 0
OK
(0.80s)
$IP:6379> KEYS *
1) "numb"
2) "temp"
3) "stor"
4) "flag"
```

## Which command is used to obtain all the keys in a database?
```
$IP:6379> KEYS *
1) "numb"
2) "temp"
3) "stor"
4) "flag"
```


## Submit root flag
- `$IP:6379> GET flag`