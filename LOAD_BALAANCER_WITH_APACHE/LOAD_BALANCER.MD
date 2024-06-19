# Load Balancer Solution With Apache

In this project we will enhance our Tooling [Website solution](https://github.com/GodwinGNS/steghub_learning/tree/master/DEVOPS_TOOLING-WS) by adding a Load Balancer to disctribute traffic between Web Servers and allow users to access our website using a single URL

Deploy and configure an Apache Load Balancer for Tooling Website solution on a separate Ubuntu EC2 intance. Make sure that users can be served by Web servers through the Load Balancer.

To simplify, let us implement this solution with 2 Web Servers, the approach will be the same for 3 and more Web Servers.

1. Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb, so your EC2 list will look like this
 
2. Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group  
   
![alt text](IMAGES/1.png)

3. Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Server

```
#Install apache2
sudo apt update
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
```
* make sure apache2 is up and running

```
sudo systemctl status apache2
```
![alt text](IMAGES/2.png)


* Configure load balancing

```
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
```

* Restart apache server
```
sudo systemctl restart apache2
```

![alt text](IMAGES/3.png)


4. In our last project we mounted 
/var/log/httpd/ from your Web Servers to the NFS server - unmount them and make sure that each Web Server has its own log directory.

```
sudo umount /var/log/httpd/

# if the prove to be busy use -l flag to unmount it

sudo umount -l /var/log/httpd
```

* Open two terminal ssh consoles for both Web Servers and run following command:

```
sudo tail -f /var/log/httpd/access_log
```

  ![alt text](IMAGES/6.png)

  * Try to refresh your browser page http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php several times and make sure that both servers receive HTTP GET requests from your LB - new records must appear in each server's log file. The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers - it means that traffic will be disctributed evenly between them.
  
  If you have configured everything correctly - your users will not even notice that their requests are served by more than one server.

  ![alt text](IMAGES/4.png)

  ![alt text](IMAGES/5.png)


  ## Optional Step - Configure Local DNS Names Resolution

 5. Sometimes it is tedious to remember and switch between IP addresses, especially if you have a lot of servers under your management. What we can do, is to configure local domain name resolution. The easiest way is to use /etc/hosts file, although this approach is not very scalable, but it is very easy to configure and shows the concept well. So let us configure IP address to domain name mapping for our LB.


```
#Open this file on your LB server

sudo vi /etc/hosts

#Add 2 records into this file with Local IP address and arbitrary name for both of your Web Servers

<WebServer1-Private-IP-Address> Web1
<WebServer2-Private-IP-Address> Web2
```

* Now you can update your LB config file with those names instead of IP addresses.

```
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```

6. You can try to curl your Web Servers from LB locally curl http://Web1 or curl http://Web2 - it shall work.

Remember, this is only internal configuration and it is also local to your LB server, these names will neither be 'resolvable' from other servers internally nor from the Internet.

![alt text](IMAGES/7.png)


with the follow 6 steps we were able to implement a load balancing web solution for our Devops team.

![alt text](/LOAD_BALAANCER_WITH_APACHE/IMAGES/image-23-768x792.png)