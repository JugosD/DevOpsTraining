# EPAM DevOps training
## (January 2017)

# Task1
1. Create github account
2. Create github repository (like devops_training)
3. Create branch task1
4. Install VirtualBox with additions/extensions
5. Install Vagrant
6. Create Vagrantfile (in “task1” branch) based on Centos7 distributive (vagrant box can be used the same as in presentation)
7. Bring up and provision several VMs (2 is enough) with “Shell” provisioner:
    * install git only on one of them
    * clone your repository on machine where git is installed  and switch to task1 branch

# Task2
1. Create Vagrantfile (in “task2” branch) based on Centos7 distributive.
2. Vagrantfile should bring up and provision three VMs:
    * 1 with Apache httpd
    * 2 with JRE + Apache tomcat
    * Create index.html (in both tomcat VMs) in /usr/share/tomcat/webapps/<some_fodler>
    * Configure load balancing
