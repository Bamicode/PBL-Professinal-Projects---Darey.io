In this project we will enhance our Tooling Website solution by adding a Load Balancer to disctribute traffic between Web Servers and allow users to access our website using a single URL.

## Task

Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

To simplify, let us implement this solution with 2 Web Servers, the approach will be the same for 3 and more Web Servers.

### Prerequisites

Make sure that you have following servers installed and configured within Project-7:

- Two RHEL8 Web Servers

- One MySQL DB Server (based on Ubuntu 20.04)

- One RHEL8 NFS server

### Configure Apache As A Load Balancer

Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so your EC2 list will look like this:

![Instaces](https://user-images.githubusercontent.com/96090546/169706831-28161f47-42f4-421e-8152-62574716087a.PNG)


Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group.

Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:

#Install apache2

    sudo apt update -y
    sudo apt install apache2 -y
    sudo apt-get install libxml2-dev

#Enable following modules:

    sudo a2enmod rewrite
    sudo a2enmod proxy
    sudo a2enmod proxy_balancer
    sudo a2enmod proxy_http
    sudo a2enmod headers
    sudo a2enmod lbmethod_bytraffic

#Restart apache2 service

    sudo systemctl restart apache2

Make sure apache2 is up and running

    sudo systemctl status apache2

## Configure load balancing

    sudo vi /etc/apache2/sites-available/000-default.conf

#Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

    <Proxy "balancer://mycluster">
                   BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
                   BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
                   ProxySet lbmethod=bytraffic
                   # ProxySet lbmethod=byrequests
            </Proxy>

            ProxyPreserveHost On
            ProxyPass / balancer://mycluster/
            ProxyPassReverse / balancer://mycluster/

#Restart apache server

    sudo systemctl restart apache2

bytraffic balancing method will distribute incoming load between your Web Servers according to current traffic load. We can control in which proportion the traffic must be distributed by loadfactor parameter.








