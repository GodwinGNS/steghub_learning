# Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins

## Task
Enhance the architecture prepared in Project 8 by adding a Jenkins server, configure a job to automatically deploy source codes changes from Git to NFS server.

Here is how your updated architecture will look upon competion of this project

![alt text](IMAGES/image-25-1024x603.png)

This project going to take 3 steps to complete.

### Step 1 - Install Jenkins server

* Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"
* Install JDK (since Jenkins is a Java-based application)

```
sudo apt update
sudo apt install fontconfig openjdk-17-jre
```

* Install Jenkins

```
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add - 
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5BA31D57EF5975CA
sudo apt update
sudo apt install jenkins -y
```

* Ensure Jenkins is properly installed and running

```
sudo systemctl status jenkins
```

![alt text](IMAGES/1..png)

* By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound Rule in your EC2 Security Group

* Perform initial Jenkins setup.
From your browser access http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080

You will be prompted to provide a default admin password

![alt text](IMAGES/2.png)

* Retrieve it from your server:
```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
![alt text](IMAGES/3.png)

* Then you will be asked which plugings to install - choose suggested plugins.

![alt text](IMAGES/4.png)

* Once plugins installation is done - create an admin user and you will get your Jenkins server address.
  
![alt text](IMAGES/5.png)

The installation is completed!

![alt text](IMAGES/6.png)

### Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, you will learn how to configure a simple Jenkins job/project (these two terms can be used interchangeably). This job will will be triggered by GitHub webhooks and will execute a 'build' task to retrieve codes from GitHub and store it locally on Jenkins server.

* Enable webhooks in your GitHub repository settings

![alt text](IMAGES/7.png)


* Go to Jenkins web console, click "New Item" and create a "Freestyle project"
* 
![alt text](IMAGES/8.png)

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

* In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

* Save the configuration and let us try to run the build. For now we can only do it manually. Click "Build Now" button, if you have configured everything correctly, the build will be successfull and you will see it under #1

![alt text](IMAGES/9.png)

open the build and check in "Console Output" if it has run successfully.

To fix build to run automatic when we triger job

* Click "Configure" your job/project and add these two configurations

* Configure triggering the job from GitHub webhook

* Configure "Post-build Actions" to archive all the files - files resulted from a build are called "artifacts"

Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results - artifacts, saved on Jenkins server.

![alt text](IMAGES/10.png)

We have now been able to configure an automated Jenkins job that receives files from GitHub by webhook trigger (this method is considered as 'push' because the changes are being 'pushed' and files transfer is initiated by GitHub). There are also other methods: trigger one job (downstreadm) from another (upstream), poll GitHub periodically and others.


![alt text](IMAGES/11.png)

By default, the artifacts are stored on Jenkins server locally

```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```

![alt text](IMAGES/12.png)


### Step 3 - Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin that is called "Publish Over SSH".

* Install "Publish Over SSH" plugin.

* On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.
On "Available" tab search for "Publish Over SSH" plugin and install it

![alt text](IMAGES/13.png)

* Configure the job/project to copy artifacts over to NFS server.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

* Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)
* Arbitrary name
* Hostname - can be private IP address of your NFS server
* Username - ec2-user (since NFS server is based on EC2 with RHEL 8)
* Remote directory - /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server


![alt text](IMAGES/14.png)

Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

Save the configuration, open your Jenkins job/project configuration page and add another one "Post-build Action"

* Configure it to send all files probuced by the build into our previouslys define remote directory. In our case we want to copy all files and directories - so we use **. If you want to apply some particular pattern to define which files to send - use this syntax.

* Save this configuration and go ahead, change something in README.MD file in your GitHub Tooling repository.

* Webhook will trigger a new job and in the "Console Output" of the job you will find something like this:

![alt text](IMAGES/15.png)

To make sure that the files in /mnt/apps have been udated - connect via SSH/Putty to your NFS server and check README.MD file


```
cat /mnt/apps/README.md
```
![alt text](IMAGES/16.png)



![alt text](IMAGES/17.png)


![alt text](IMAGES/17.png)


We have just implemrnted our first continous Integration solution using Jenkins CI. In our next project we are going to advance the CI configuration.