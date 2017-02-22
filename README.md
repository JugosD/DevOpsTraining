# EPAM DevOps training `(January 2017)`

## Task1
1. Create github account
2. Create github repository (like `devops_training`)
3. Create branch `task1`
4. Install VirtualBox with additions/extensions
5. Install Vagrant
6. Create Vagrantfile (in [task1](https://github.com/JugosD/devops_training/tree/task1) branch) based on Centos7 distributive (vagrant box can be used the same as in presentation)
7. Bring up and provision several VMs (2 is enough) with `Shell` provisioner:
	- install git only on one of them
	- clone your repository on machine where git is installed and switch to `task1` branch

## Task2
1. Create Vagrantfile (in [task2](https://github.com/JugosD/devops_training/tree/task2) branch) based on Centos7 distributive.
2. Vagrantfile should bring up and provision three VMs:
	- 1 with Apache httpd
	- 2 with JRE + Apache tomcat
	- Create `index.html` (in both tomcat VMs) in `/usr/share/tomcat/webapps/<some_fodler>`
	- Configure load balancing

## Task3
1. Install Jenkins and Nexus OSS
2. Create gradle build script for sample project (`build.gradle`)
	- Web application should have version (`gradle.properties`)
	- Create increment version task
	- Create task to populate `greetings.txt` file with version (in build directory)
3. Create Jenkins job:
	- Clone your repository (task3 branch)
	- Build war file with gradle (increment version,
	push changes on github, upload war file to nexus) nexus path :
	`training/task3/1.0.xxx/task3.war`
	- Stop LB tomcat1, download war from nexus, deploy into tomcat1,
	verify it is deployed correctly (check version), start LB tomcat1
	- Do the same for LB tomcat2
	- Commit Jenkinsfile (in [task3](https://github.com/JugosD/devops_training/tree/task3) branch)

## Task4
1. Create [task4](http://github.com/JugosD/devops_training/tree/task4) branch based on [task3](https://github.com/JugosD/devops_training/tree/task3)
2. Create and commit `Dockerfile` based on tomcat image:
	- Use ARG to download appropriate version of war
	- Download war from nexus
3. Update `Jenkinsfile`:
	- Increment version, build war, publish to nexus (should be already done)
	- Build docker image and publish it to docker registry `${host}:5000/${image}:${version}`  
	(e.g. `localhost:5000/task4:2.0.0`)
	- On some another machine get image from docker registry pull and run it, perform deploy validation (curl or http request)
	- Stop and remove container

## Task5
1. Install Chef server (on any machine)
2. Install and configure `chefdk` on Jenkins machine
3. Add nodes to chef server
4. Create cookbooks: 
	- For installing docker (configure daemon to use `insecure-registry` option)
	- For running container downloaded from docker registry
5. Commit cookbooks into [task5](http://github.com/JugosD/depops_training/tree/task5) branch
- Note: do not hardcode urls, versions, etc. (move it to attributes)
