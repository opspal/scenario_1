## Typical steps to configure a fresh CentOs server

1. Set a host name for the host (Optional in Vagrant setup)
    ```
    hostnamectl set-hostname web01.qa.vegspot.ag
    ``` 

2. Set IP address for the network interfaces. (Optional in Vagrant setup)
    ```
    # display devices
    [root@web01 ~]# nmcli d
    DEVICE  TYPE      STATE      CONNECTION  
    eth0    ethernet  connected  eth0 
    eth1    ethernet  connected  eth1
    lo      loopback  unmanaged  --

    # set IPv4 address ⇒ nmcli *** [IP address]
    [root@web01 ~]# nmcli c modify eth1 ipv4.addresses 192.168.56.10/24

    # set default gateway
    [root@web01 ~]# nmcli c modify eth1 ipv4.gateway 192.168.56.1/24

    # set DNS
    [root@web01 ~]# nmcli c modify eth1 ipv4.dns 192.168.56.100/24

    # set manual for static setting (it's "auto" for DHCP)
    [root@web01 ~]# nmcli c modify eth0 ipv4.method manual

    # restart the interface and reload the settings
    [root@web01 ~]# nmcli c down eth1; nmcli c up eth1
    Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/1)
    ```

3. Update the packages to the latest versions
    ```
    yum update -y
    ```

4. Make SELinux to Permissive (Temporarily) 
    ```
    setenforce 0
    ```
    PS: It is not recommended to run a CentOs server in Permissive mode, However if you are a beginner, Running SELinux in Enforced mode might introduce additional complexity. Just keep things simple for now.

    [More on SELinux](https://gist.github.com/thisara-soori/406e14a212803e201924c97c5df06c6d)

5. Start firewalld Service if not running
    ```
    [root@web01 ~]# systemctl status firewalld
    ● firewalld.service - firewalld - dynamic firewall daemon
    Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
    Active: inactive (dead)
        Docs: man:firewalld(1)
    [root@web01 ~]# systemctl start firewalld
    ```



## TASK: Configure Apache Server

1. Install Apache webserver
    ```
    yum install httpd -y
    ```
2. Check the Service status
    ```
    [root@web01 ~]# systemctl status httpd
    ● httpd.service - The Apache HTTP Server
    Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
    Active: inactive (dead)
        Docs: man:httpd(8)
            man:apachectl(8)
    ```
3. Start the `httpd` service
    ```
    [root@web01 ~]# systemctl start httpd
    ```
4. Enable http traffic on firewall and reload firewall 
    ```
    [root@web01 ~]# firewall-cmd --add-service=http --permanent
    success

    [root@web01 ~]# firewall-cmd --reload
    success
    ```
5. Check the IP address on the browser, make sure the Apache default welcome page is loading.

6. Install git and clone the repository.
    ```
    [root@web01 ~]# cd /opt/
    
    [root@web01 opt]# yum install git -y
    
    [root@web01 opt]# git clone https://github.com/opspal/scenario_1.git
    ```

7. Copy document root to `/var/www/html/`
    ```
    [root@web01 scenario_1]# cp -r vegspot.ag /var/www/html/
    ```

8. Modify the apache configuration file
    ```
    vim /etc/httpd/conf/httpd.conf

    # line 86: change to administrator email address
    ServerAdmin root@vegspot.ag

    # line 119: change   
    DocumentRoot "/var/www/html/vegspot.ag"
    
    # line 151: change
    AllowOverride All
    ```

9. Restart httpd server
    ```
    [root@web01 ~]# systemctl restart httpd
    ```
