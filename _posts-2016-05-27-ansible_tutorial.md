---
layout: post
title: "Ansible Tutorial"
date: 2016-07-11
---

## Installation
``` 
sudo apt-add-repository -y ppa:ansible/ansible
sudo apt-get update
sudo apt-get install -y ansible
```

### Configurations
##### Host Configurations
1. Management of servers is done in */etc/ansible/hosts*
2. The square bracket *[]* is used to define the group or label the servers

##### Playbook Configurations


##### Quick Commands
1. *ansible all -m ping* <br>
Sample output
```
127.0.0.1 | success >> {
    "changed": false,
    "ping": "pong"
}
```

2. *ansible all -m ping -s -k -u user* <br>
-s : sudo permissions
-k : provide key for the user to login
-u : username to use for logging in

*-a : to run ad hoc commands* 
