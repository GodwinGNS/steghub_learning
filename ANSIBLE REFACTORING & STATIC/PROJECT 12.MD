# Ansible Refactoring & Static Assignments (Imports and Roles)

In this project, you will continue working with ansible-config-mgt repository and make some improvements of your code.

## Step 1 - Jenkins job enhancement

1. Go to your Jenkins-Ansible server and create a new directory called ansible-config-artifact - we will store there all artifacts after each build.

```
sudo mkdir /home/ubuntu/ansible-config-artifact
```

2. Change permissions to this directory, so Jenkins could save files there - chmod -R 0777 /home/ubuntu/ansible-config-artifact

![alt text](IMAGES/1.png)

3. Go to Jenkins web console -> ``Manage Jenkins -> Manage Plugins -> on Available `` tab search for Copy Artifact and install this plugin without restarting Jenkins.

![alt text](IMAGES/2.png)

4. Create a new Freestyle project like we did in project 9 Project 9 and name it save_artifacts.

5. This project will be triggered by completion of your existing ansible project. Configure it accordingly:

![alt text](IMAGES/3a.png)

**Note** It is important to keep the number of your build to 2. This will keep the last 2 build and keep order in the build space.

6. The main idea of save_artifacts project is to save artifacts into /home/ubuntu/ansible-config-artifact directory. To achieve this, create a Build step and choose Copy artifacts from other project, specify ansible as a source project and /home/ubuntu/ansible-config-artifact as a target directory.


![alt text](IMAGES/3b.png)

7. Test your set up by making some change in README.MD file inside your ansible-config-mgt repository (right inside master branch).

![alt text](IMAGES/4.png)

![alt text](IMAGES/5.png)

If both Jenkins jobs have completed one after another - you shall see your files inside /home/ubuntu/ansible-config-artifact directory and it will be updated with every commit to your master branch.


## Step 2 - Refactor Ansible code by importing other playbooks into site.yml

Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and create a new branch, name it ``refactor``.

![alt text](IMAGES/6.png)

DevOps philosophy implies constant iterative improvement for better efficiency - refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

In previous project, you wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of OS, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements. In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain server/OS families. Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

Most Ansible users learn the one-file approach first. However, breaking tasks up into different files is an excellent way to organize complex sets of tasks and reuse them.

Let see code re-use in action by importing other playbooks.

1. Within playbooks folder, create a new file and name it ``site.yml``

2. Create a new folder in root of the repository and name it ``static-assignments``. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. 

3. Move ``common.yml`` file into the newly created ``static-assignments`` folder.

![alt text](IMAGES/7.png)

4. Inside site.yml file, import common.yml playbook.

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```
![alt text](IMAGES/8.png)

5. Run ansible-playbook command against the dev environment

Since you need to apply some tasks to your dev servers and wireshark is already installed - you can go ahead and create another playbook under static-assignments and name it common-del.yml. In this playbook, configure deletion of wireshark utility.

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```

![alt text](IMAGES/9.png)

update site.yml with - import_playbook: ../static-assignments/common-del.yml instead of common.yml and run it against dev servers.

```
cd /home/ubuntu/ansible-config-mgt/

ansible-playbook -i inventory/dev.yml playbooks/site.yaml
```

**NOTE**: Jenkins Job Creation : in a situation where you get an access denied from your jenkins save artifact job when trying to build , this is caused by jenkins not been able to access the specified folder to save the artifacts.

To solve this run:
```
sudo usermod -a -G ubuntu jenkins

 # verify jenkins has been added to the group
 groups jenkins
```

The above will add jenkins to the same sudo group as ubuntu.

this will followed by restarting jenkins

```
sudo systemctl restart jenkins
```
![alt text](<IMAGES/10 connected to save artifact.png>)

Make sure that wireshark is deleted on all the servers by running wireshark --version

![alt text](IMAGES/11.png)

![alt text](IMAGES/15.png)

![alt text](IMAGES/16.png)


## Step 3 - Configure UAT Webservers with a role 'Webserver'

1. Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so give them names accordingly - Web1-UAT and Web2-UAT.

![alt text](IMAGES/17.png)

2. To create a role, you must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.

**NOTE:** There are two ways to create a folder structure.
* Use an Ansible utility called ansible-galaxy inside ansible-config-mgt/roles directory
```
mkdir roles && cd roles
ansible-galaxy init webserver
```

* Create the directory/files structure manually

since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on Jenkins-Ansible server.

The entire folder structure should look like below, but if you create it manually - you can skip creating tests, files, and vars or remove them if you used ansible-galaxy

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```

After removing unnecessary directories and files, the roles structure should look like this

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    └── templates
```

![alt text](IMAGES/18.png)

3. Update your inventory ansible-config-mgt/inventory/uat.yml file with IP addresses of your 2 UAT Web servers

Ensure you are using ssh-agent to ssh into the Jenkins-Ansible instance just as you have done in project 11

```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
```

![alt text](IMAGES/19.png)

4. In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.

**NOTE:** that you need to create ansible in /etc/ansible/ansible.cfg manually.

add this defaults settings:

```
sudo /etc/ansible/ansible.cfg

[defaults]
roles_path = /home/ubuntu/ansible-config-artifact/roles
```
make sure that your ansible.cfg shows when you when you check for ```ansible --version```

5. It is time to start adding some logic to the webserver role. Go into tasks directory, and within the main.yml file, start writing configuration tasks to do the following

* Install and configure Apache (httpd service)
* Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
* Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
* Make sure httpd service is started

the main.yml will consist of the following tasks

```
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/GodwinGNS/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```
![alt text](IMAGES/20.png)

## Step 4 - Reference 'Webserver' role

Within the ```static-assignments``` folder, create a new assignment for uat-webservers ```uat-webservers.yml```. This is where you will reference the role.

```
---
- hosts: uat-webservers
  roles:
     - webserver
```
Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your uat-webservers.yml role inside site.yml.

So, we should have this in site.yml

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml
```

![alt text](IMAGES/21.png)

## Step 5 - Commit & Test

Commit your changes, create a Pull Request and merge them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your Jenkins-Ansible server into /home/ubuntu/ansible-config-mgt/ directory.

Now run the playbook against your uat inventory and see what happens

```
cd /home/ubuntu/ansible-config-artifact

ansible-playbook -i /inventory/uat.yml playbooks/site.yaml
```
![alt text](IMAGES/21b.png)

You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser

```
http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

or

http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php
```
![alt text](IMAGES/23.png)


Your Ansible architecture now looks like this:
![alt text](image-53-768x752.png)

