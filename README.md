# Deploy mongodb replica set cluster with authentication / authorization

## Typical usage with vagrant boxes

    1. Set ip in of the vagrant boxes in inventory/test.ini
    2. Run: ansible-playbook -i inventory/test.ini -k -u vagrant -s init.yml
    3. Run: ansible-playbook -i inventory/test.ini main.yml

Note: other options are detailled below to bootstrap this cluster on non vagrant boxes

## Inventory

The inventory/ENVIRONMENT.ini file defines the hosts (primary and secondaries) used for the replica set.  
On top the host decalration, some databasqe parameters are defined

eg:
    [primary]
    192.168.1.200

    [secondary]
    192.168.1.201
    192.168.1.202

    [primary:vars]
    db_user_admin_password=ADMINPASSWORD
    db_root_admin_password=ROOTPASSWORD
    db_name=DBNAME
    db_user_name=DBUSERNAME
    db_user_password=DBUSERPASS

## Nodes initialisation

This first task initiate the server creating a user named mongors
* mongors user will be given sudo right with no password needed when running sudo commands
* current machine ssh key is copied over to the authorized_keys of the server that is beeing provisionned

Several possible cases:

* Vagrant VM are used for test => vagrant user needs to be used

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -k -u vagrant -s init.yml```

Note: vagrant ssh password requested (sudo password not requested as vagrant user is authorized to sudo without password)

* root access is provided => root user needs to be used

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -k -u root init.yml```

Note: root ssh password will be requested

* another user provided with sudo right and a password needed to issue sudo commands

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -k -K -u USER -s init.yml```

Note: both user's ssh password + sudo password will be requested

* root access provided through an ssh key (no need to enter a password)

  ```ansible-playbook -i inventory/ENVIRONMENT.ini -u root -s init.yml```

Note: this option is usefull in the case the host is created by specifying a ssh key (AWS, DO, ...)

## Replica set setup

Once the nodes are registered, the replica set can be created using the following command:

```ansible-playbook -i inventory/ENVIRONMENT.ini main.yml```
