# trusty-pacemaker-demo
Corosync-Pacemaker-HAProxy cluster demo for Ubuntu trusty by Ansible and Vagrant.

## Abstract

This Vagrantfile will deploy the Corosync-Pacemaker-HAProxy cluster on Ubuntu 14.04 LTS virtual machines with VirtualBox.
The HAProxy controls 3 of nginx service.

## Requirements

My development environment is shown below. I think that Ubuntu LinuxBox also works.

    $ cat /etc/redhat-release
    CentOS Linux release 7.1.1503 (Core)
    $ uname -a
    Linux zouk.local 3.10.0-229.4.2.el7.x86_64 #1 SMP Wed May 13 10:06:09 UTC 2015 x86_64 x86_64 x86_64 GNU/Linux
    $ cat /proc/meminfo | head -6
    MemTotal:       16243556 kB
    MemFree:          153060 kB
    MemAvailable:    7363000 kB
    Buffers:            4076 kB
    Cached:          7484732 kB
    SwapCached:        15380 kB
    $ vboxmanage --version
    4.3.28r100309
    $ vagrant --version
    Vagrant 1.7.2
    $ ansible --version
    ansible 1.9.1
      configured module search path = None
    $ vagrant box list
    ubuntu/trusty64 (virtualbox, 0)

My Vagrant base box 'ubuntu/trusty64' obtained from the following.
https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box

## How to use

1. Run `vagrant up` and `vagrant reload`
1. Login to *starter* by `vagrant ssh starter`
1. You get the response of the nginx with the following command.

    ```
    vagrant@starter:~$ curl http://sv/
    ```

1. The following message is a successful example.

    ```
    vagrant@starter:~$ curl http://sv/
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to sv1</title>
    </head>
    <body>
    </body>
    </html>
    vagrant@starter:~$ curl http://sv/
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to sv2</title>
    </head>
    <body>
    </body>
    </html>
    vagrant@starter:~$ curl http://sv/
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to sv3</title>
    </head>
    <body>
    </body>
    </html>
    vagrant@starter:~$
    ```

    It can be seen that the server is switched every time you use curl command.

1. Try the resources to stop. Enter the following command.

    ```
    vagrant@starter:~$ ssh sv1 sudo crm_mon -rfA1
    Last updated: Sun Jun  7 03:02:08 2015
    Last change: Sun Jun  7 02:58:40 2015 via cibadmin on sv3
    Stack: corosync
    Current DC: sv1 (167772497) - partition with quorum
    Version: 1.1.10-42f2063
    3 Nodes configured
    2 Resources configured


    Online: [ sv1 sv2 sv3 ]

    Full list of resources:

     Resource Group: g_services
         p_vip      (ocf::heartbeat:IPaddr2):       Started sv1
         p_haproxy  (ocf::heartbeat:haproxy):       Started sv1

    Node Attributes:
    * Node sv1:
    * Node sv2:
    * Node sv3:

    Migration summary:
    * Node sv1:
    * Node sv2:
    * Node sv3:
    vagrant@starter:~$
    ```

    Because it is displayed as `Started sv1`, and stops the sv1.

    ```
    vagrant@starter:~$ ssh sv1 sudo poweroff
    vagrant@starter:~$
    ```

1. You get the response of the nginx with the following command again.

    ```
    vagrant@starter:~$ curl http://sv/
    ```

1. The following message is a successful example.

    ```
    vagrant@starter:~$ curl http://sv/
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to sv2</title>
    </head>
    <body>
    </body>
    </html>
    vagrant@starter:~$ curl http://sv/
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to sv3</title>
    </head>
    <body>
    </body>
    </html>
    vagrant@starter:~$ curl http://sv/
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to sv2</title>
    </head>
    <body>
    </body>
    </html>
    vagrant@starter:~$ curl http://sv/
    <!DOCTYPE html>
    <html>
    <head>
    <title>Welcome to sv3</title>
    </head>
    <body>
    </body>
    </html>
    vagrant@starter:~$
    ```

    Except you stopped the server returns a response.

## Restrictions

1. Note the version of libqb0. 0.17 or higher is required.

## Thanks

* https://github.com/stackforge/fuel-library/blob/master/deployment/puppet/cluster/files/haproxy
* https://github.com/russki/cluster-agents/blob/master/haproxy
