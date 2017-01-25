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

# Task3
1. Install Jenkins and Nexus OSS
2. Create gradle build script for sample project (build.gradle)
  * Web application should have version (gradle.properties)
  * Create increment version task
  * Create task to populate greetings.txt file with version (in build directory)
3. Create Jenkins job:
  * Clone your repository (task3 branch)
  * Build war file with gradle (increment version, push changes on github, upload war file to nexus) nexus path : training/task3/1.0.xxxx/task3.war
  * Stop LB tomcat1, download war from nexus, deploy into tomcat1, verify it is deployed correctly (check 	version), start LB tomcat1
  * Do the same as in item 3 for LB tomcat2
  * Commit Jenkinsfile in task3 branch
