Deployment of a mongodb replicaset with authN / authZ
-----------------------------------------------------

This playbook setup a MongoDB replicaset with
* user authentication / authorization
* secure communication between the nodes (using key file).

Inventory
---------

The inventory/ENVIRONMENT.ini file defines the hosts (primary and secondaries) used for the replicaset.  
On top the host decalration, some databasqe parameters are defined

```
[primary]
PRIMARY_IP

[secondary]
SECONDARY_1_IP
SECONDARY_2_IP

[primary:vars]
db_user_admin_username=USER_ADMIN_USERNAME
db_user_admin_password=USER_ADMIN_PASSWORD
db_cluster_admin_username=CLUSTER_ADMIN_USERNAME
db_cluster_admin_password=CLUSTER_ADMIN_PASSWORD
db_user_name=DB_USERNAME
db_user_password=DB_PASSWORD
db_name=DB_NAME
```

Nodes initialisation
--------------------

This first task initiates the server creating a user named mongors
* mongors user will be given sudo right with no password needed when running sudo commands
* current machine ssh key is copied over to the authorized_keys of the server that is beeing provisionned

Note: for this last item, make sure you have a file id_rsa.pub in your home directory. If not you can create one with ```ssh-keygen -t rsa```

Several possible cases depending upon the way the nodes are created:

* Vagrant VM are used for test => **vagrant** user needs to be used

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -k -u vagrant -s init.yml```

Note: vagrant ssh password requested (sudo password not requested as vagrant user is authorized to sudo without password)

* root access is provided => **root** user needs to be used

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -k -u root init.yml```

Note: root ssh password will be requested

* another user provided with sudo right and a password needed to issue sudo commands

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -k -K -u USER -s init.yml```

Note: both user's ssh password + sudo password will be requested

* user provided with a ssh key added to its authorized_keys

   ```ansible-playbook -i inventory/ENVIRONMENT.ini -u USER --private-key PATH_TO_KEY -s init.yml```

* root access provided through an ssh key (no need to enter a password)

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -u root -s init.yml```

Note: this option is usefull in the case where a ssh key is used when creating the host (AWS, DO, ...)

Replicaset setup
----------------

Once the nodes are registered, the replicaset can be created using the following command:

```ansible-playbook -i inventory/ENVIRONMENT.ini main.yml```

Connection to the Replicaset
----------------------------

```
$ mongo --ssl --sslAllowInvalidCertificates --host rs0/PRIMARY_IP,SECONDARY_1_IP,SECONDARY_2_IP
use DB_NAME
db.auth("DB_USERNAME", "DB_PASSWORD")
db.test.insert({ok:1})
```

License
-------

The MIT License (MIT)

Copyright (c) [2016]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
