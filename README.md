user_inventory - shows user and group information on hosts

Author: Andrey Klychkov <aaklychkov@mail.ru>

Version: 1.1

Date: 2018-05-29

### Requirements:
```Python3+, paramiko, OpenSSH server on targeted hosts, works with Linux only```


### Description:
Gets (by ssh) and shows user and group info on hosts from an Ansible-like inventory file.

It uses the /etc/ansible/hosts file by default and can be redefined by the --host-file=/path/ argument

The hosts file must be in the following format:
```
[host_group1]
hostname1
hostname2

[host_group2]
192.168.0.1
hostname2
hostname3
...
```
Allows to get:

1) List of users with uid >500 (min uid may be passed by --min-uid UID)

2) List of members of a desired group

3) User info (uid, group membership)

Use --csv argument for getting output in csv format.

### Synopsis:
```
user_inventory [-h] -u USER [-p PORT] [--hosts-file HOSTS_FILE]
               [--min-uid UID] [--csv]
               [--host-group HOST_GROUP | --host HOSTNAME]
               [--show-users | --check-user USERNAME | --check-group GROUPNAME | --version]
```

**Options:**
```
  -h, --help            show this help message and exit
  -u USER, --user USER  ssh user to connect
  -p PORT, --port PORT  ssh port to connect
  --hosts-file HOSTS_FILE
                        alternative 'hosts' ini file ('hosts' in the current dir
                        by default)
  --min-uid UID         min user uid for inventory
  --csv                 csv format output
  --host-group HOST_GROUP
                        host group from 'hosts' file for inventory
  --host HOSTNAME       examine the specific host
  --show-users          show users on hosts
  --check-user USERNAME
                        check user on hosts
  --check-group GROUPNAME
                        check group on hosts
  --version             show version and exit

```


**Examples:**

1) Show list of users for each host in the 'hosts' file:
```
./user_inventory -u <ssh_user> --show-user
```
Output:
```
===============
host: hostname1
===============
user0           2003
test1           2004    /sbin/nologin
test2           2005    /sbin/nologin
test3           2006    /sbin/nologin
...
```
with --csv:
```
hostname1,bin,1,/sbin/nologin
hostname1,daemon,2,/sbin/nologin
hostname1,adm,3,/sbin/nologin
hostname1,lp,4,/sbin/nologin
hostname1,sync,5,/bin/sync
hostname2,shutdown,6,/sbin/shutdown
hostname2,halt,7,/sbin/halt
hostname2,mail,8,/sbin/nologin
hostname2,operator,11,/sbin/nologin

```

2) Show list of users for each host in host_group1 with uid >1:
```
./user_inventory -u <ssh_user> --show-user --host-group host_group1 --min-uid 1
```

3) If user 'andrew' exists on host 'hostname1', show user info in csv format
```
./user_inventory -u <ssh_user> --check-user andrew --host hostname1 --csv
```
Output:
```
hostname1,andrew,2002,2002(andrew),10(wheel)
```
4) Show users who is a members of wheel on host 'hostname1':
```
./user_inventory -u <ssh_user> --check-group wheel --host hostname1
```
Output
```
host:hostname1 group:wheel members:andreyw,test1
```
