# Practical Jenkins
21 Marzo 2018 10:49

## Section 1
### Section 1.1
We are going to take a look at…
		* What it is Jenkins
		* What are CI, CD and CDelivery
		* Configure Jenkins
			* How to install Jenkins
			* Dependencies
			* Automate the process
		* Jenkins Features
		* Development Stages
		* Advances features

	What it is? Created by Kahsuke Kawaguchi Sun Microsystems: Application for routine development cycle functions.

Development Lifecycle
	Forked a branch, add and modify  code, test code, merge to main branch, execute test, merge to master branch, optionally create a tag for deployment, the package is deployed by Jenkins…

::This isn’t clearly for me, probably it could be more clearly if the autor draw a scheme for show this steps::

Advanced Features
* Flexibility configuration
* API available
* Distributed builds
* Multibranch support
* User friendly interfaces

### Section 1.2

Continues Integration
- Development practice
- New feature is added to integration branch
- Tested before
- Errors detected early
- Testing automatically without manual intervention
- New code is added to the integration branch

Continues Delivery
- Getting code into a deployment state
- Makes sure that all changes are ready to be deployed at any time
- Low risk, and depends for CI phase

Continues Deployment
- Automatic releases to production
- Level of testing decides how to good the release will be
- Release happens in small batches and continuously
- Gradual product improvement

::Same comment! Isn’t clearly for me!::

### Section 1.3

Setting up Environment
- Git Configuration
- Github Account
- Dedicated storage
- JVM

Use Vagrant with CentOs Image.

> yum -y install git

::I have to spend time setting up my environment with Vagrant for follow the video steps::

Install Git and adding the ssh key from CentOS Vagrant

::Isn’t clearly what we are use Amazon EC2::

Install Open JDK 8 in Vagrant

### Section 1.4

Install Jenkins by SO Package
Install Jenkins by war in tomcat ::easy::
Install Jenkins by Docker Containers

- Install Docker Community Edition in CentOs
- Start Docker
- Install Docker Image and run it
- You will see the log and the first password
- For stop

> docker pull Jenkins/jenkins:lts

> docker run —name jenkins_master -p 8080:8080 -p -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts


::I spent time configure my SO with Vagrant (permissions)::

::Can I use the same Vagrant Machine for make the three installations? Is that recommend?::

::Docker container explain isn’t clearly, it’s too much::

### Section 1.5

Install nginx (okas)

Install Jenkins by the cli (set git account and credentials)

### Section 1.6

- Automate the Init
Disable wizard for configure Jenkins by the first attempt. You can create a Groovy Script for create the first account (include Jenkins and Hudson libraries)
Create other groovy script for install plugins.

::Here doesn’t specified the groovy scripts::

Set permissions
Restart Jenkins

- Puppet

Add puppet labs repo to CentOs
Install puppet
> yum -y install puppet-agent

Logout and login again to the SO
> sudo su -

Create a structure folder
- puppet
	- modules
		- Jenkins
			- manifests
				- install.pp (puppet configuration for install Jenkins, java, nginx and git)
				- config.pp ( direct to groovy scripts for configure Jenkins and nginx)
				- service.pp (configure services for Jenkins and nginx)
				- init.pp (refer previous files created in sequence)
			- files
				- installPlugins.groovy
				- security.groovy
				- Jenkins
				- Jenkins.conf
				- nginx.conf
	- manifests
		- site.pp (create a node for include Jenkins module)

Run puppet
> puppet apply —module path ./modules manifests/site.pp
::Isn’t clearly for me!::

### Section 1.7
Create a simple test with python

Job by the UI:
* Create a job (free-style)
* Select source code git, and put the url and credentials
* Build: execute shell, put python command for test
* Build now

Job by Cli:
- create a folder and copy
> cp /var/lib/jenkins/jobs/python-job/config.xml .

- Adding to the end of the xml some nodes xml <publisher>
- Curl to the Jenkins API ::Crumb Issuer:: for generate a crumb issuer (return token)
- POST request to Jenkins API for create ITEM
- See the new job created

## Section 2
### 2.1 high availability
Index of all section
How to conectar two Jenkins and sincronice jobs
Isn’t clearly because it uses AWS

### 2.2 backup
How to install a plugin and how to configure

### 2.3 Monitoring components and data

How to install and configure Java Melody: Monitoring Jenkins Master

Install graphite-web, MySQL, mariaDB server, Python and Cairo and more in CentOs, start mariadb, create a database, create a user and set privilegies for MySQL, and set in Graphite Configuration. Set more permissions, set headers, and run a python command. Start Carbon Cache.

Download Grafana: add the repo and install.

Install plugin Metrics Graphite Reporting at Jenkins. Configure: add the ip in Jenkins.

Start Grafana Server localhost:3000

Enter with the username set, and create a data source.

Find in Grafana web site/ dashboards: Jenkins Performance and health overview and copy the ID and add to our Grafana server.

Install Monit

Install with yum and configure.

Permissions on email login

Adding configuration to Monit

Start Monit process by terminal

Stop Jenkins by kill the process, and Monit are going to up Jenkins and send an email

### 2.4 Implementing Roles and Security

Best practices:
- disable job execution on the master
- Job restrictions
- CSRF protection in API request
- Enable the slave to master access control

Go to Manage Nodes for restrictiva the jobs
> What is a node?
The best option for security is LDAP option for isolate project permissions
Install Role Based Authorization Strategy Plugin in Jenkins
This show a matriz with users and permissions. Every job can have their own matrix role.
Show how to configure LDAP scheme security, this isn’t necessary
Show how to connect Jenkins with the LDAP
Show how to create roles in the matrix role

### 2.5 Use the API
Generate the API token in Jenkins cli

Curl for show the current jobs

Generate a Crumb for start a job

WGet the Jenkins Cli Jar and run it

It requires the token

The Jenkins cli allows a lot of things

## Section 3 Implementing Distributed build architectures and code deployments in Jenkins
## Section 3.1
Talk about what we are going to do in this chapter. It’s a informative Course:

Distributed Architecture of Jenkins
- Offloading job and builds to other nodes
- Variable environments for different types of projects
- Security measure by isolating builds from master
- Performing nodes as a cluster
- Running costs by running on demand slaves nodes

Types of Jenkins Slaves
- Physical and dedicated slaves
- Virtual machines on physical servers
- Containers on dedicated hosts such as Docker
- Cluster orchestation

Agent Launch Methods
- Java Web Start via JNLP File
- Execution command
- SSH
- Windows services

Labels and their configuration
- Confine jobs to specific nodes/slaves
- Slaves can help support environments
- When configuring slaves, a label is added to each type of slave
- In the job configuration, we add the label of the slave
- Cloud and container are added as “Cloud”

::I have to visit [Distributed builds - Jenkins - Jenkins Wiki](https://wiki.jenkins.io/display/JENKINS/Distributed+builds) for understand::

### Section 3.2 Setting up Jenkins Slaves to run build jobs

We are going to:
> Setting up slaves node with basic dependencies
> Configure ssh credentials
> Adding slave node on the Jenkins interface
> Configure labels and running builds on the slave nodes

In a new machine, in my terminal:
> Install git, and java 8
> create a new user “Jenkins”
> Generate ssh key
> Create directory .jenkins and copy the ssh public key as “jenkins_slave”
> Set permissons
> Change to the new user login
> Create a the .ssh directory and put the ssh key previously generated
> Make a wget request to `http://192.168.33.215/jnlpJars/agent.jar`

::Jenkins must be running on at this point in other machine::

In the Jenkins interface:

> Go to global security
> Enable the Agents/TCPPort Fixed Option to 5000
> Go to manage nodes
> Add New Node named “Jenkins-slave” and configure:
> - # of executors: 2
> - Remote root directory: /home/jenkins
> - Labels: jerkins-ssh-slave
> - Usage: Only build jobs with lave expressions matching with this node
> - Launch Method:  Launch via execution of command on the master
> - Launch Command: `ssh -i /opt/.jenkins/jenkins_slave jenkins@192.168.33.213 java -jar /opt/agent.jar`
> Adding other option indeed of `-i` `-o StrictHostKeyChacking=no`

Then you will see a new Jenkins Slave in `Nodes`

> Go to the jobs and configure
> Select in General Options
> - Restrict where this project can be run
> - Label Expression: `jenkins-ssh-slave`
> Save and Build
> Check that the console log says `building on remote slave…`

### Section 3.3 Using Amazon EC2

> Configuring AWS IAM
> Installing AWS EC2 Jenkins plugin
> Configure plugibn
> Running builds on AWS

Go to your AWS Dashboard
> Go to the IAM Dashboards and create a new group called `Jenkins`
> Select Full access policy
> Add new user `jenkins` and add programatic access
> Download the credentials
> Go to the EC2 console
> Create a new Key-Pair called `jenkins`

Go your Jenkins
> Install `Amazon EC2 Plugin`
> Go to credentials and add a new credential
> This would be AWS Credential
> Add the credential downloaded
> Go to manage Jenkins, and Configuration
> Add in `Cloud`a new Cloud names `aws-jenkins` and add the credentials.
> Select eu-west-1 in region
> Add the ssh key
> And test connection
>
> Add AMI ID
> - Set AMI ID
> - Add labels
> - Set usages
> - Init Script
> - User Data: Installing java and git
>
> Go to the job
> Configure and changes Label Expressions
> See the Jenkins Log for see errors
> Built the job
> And the EC2 Instance cloud have to respond for build
> This could be monitoring by the log
> Log in `tail -f /var/log/jenkins/jenkins.log`

### Section 3.4: Using Docker Containers as Jenkins Slaves

> Go to docker website  and search `cloudbess/jnpl-slave-with-java-build-tools`

> Go to your terminal with Machine Docker Host
> It have to run Docker Service

> In Jenkins Machine:
> Use Jenkins-cli for install docker plugin and restart Jenkins
> Go the Jenkins UI
> In configuration add a New Cloud (Docker Cloud)
> Add the name, and the URI
> Add docker agent template
> Connect by JNPL

> Go to the job and change the label to the docker-slave
> Build the job
>

### Section 3.5 Running containers as Jenkins Slaves on Kubernetes Platform

> How Kubernetes runs Jenkins Slaves
> Installing kubernetes plugin
> Configure Jenkins credentials
> Configure Kubernetes Plugin
> Running Slaves on the Kubernetes Platform

> Install `kubernetes` plugin by Jenkins-cli (Jenkins Machine)

> Kubernetes Machine (you have to install already)
> cat ./kube/config for see the credentials
> Copy the `Certificate-authority-data`, `Client Certificate-data`, and `Cliente Key Data`

> In Jenkins Machine:
> Create a cube directory on home
> echo the kubernetes credentials `echo jdfewhfi4 | base64 -d > ca.crt`
> (this would be `ca.ctr`, `client.ctr` and `client.key` )

> In Jenkins UI
> Add a Credentials in Jenkins Global Configuration
> Kind: Certificate
> Certificate: /opt/kube/cert.pfx

> In Jenkins UI
> Add a new Configuration/Cloud
> Add Kubernetes Cloud
> Set the name, URL, Server Certificate key
> Choose the credential previously created
> Add a new Pod Template
> Set the label and slave name

> In the Jenkins UI
> Set in the job the kubernetes label

### Section 3.6 Deploying Code from Jenkins using AWS Code Deploy

In this section
> Understand code deployments
> Configure AWS infrastructure
> Installing plugin
> Deploy code to AWS using the CodeDeploy Plugin

Code Deployment
> Method of getting tested code
> Gets complex when the number of target systems increases
> Can be done by Jenkins or by triggering a deployment event
> Needs to be performed with as less impact to users as possible

Types of Code Deployment
> Rolling Updates:
> Canary Deployments
> Blue-Green Deployments

AWS CodeDeploy
> Adding new User in AWS AIM with `AWS Code Deploy Role`
> Go to the CodeDeploy dashboard and start with Custom Deployment
> Create a new application
> Select In-Place Deployment
> In Environment Configuration select `Amazon EC2 Instances` and add the application (Key, Value, 2 instances)
> In Service Role select the user created in AIM
> Create the application

Jenkins Machine
> Add the plugin `codedeploy`
> Clone in home a simple website repo.
> In this repo create a new file named app `spec.yml`
> Add some configuration: OS, files, hooks: beforeInstall and afterInstall
> Create a directory into the repo and create two scripts (hooks scripts)
> Commit the changes.

Jenkins UI
> Create new job “sample-website”
> Select the repository, add credentials and select Post-built actions: `AWS CodeDeploy`
> Add the application name, group and config
> Add the AWS Access key and Secret Key
> Save and build the project

## Section 4 Understanding and Implementing Build Pipelines
### Section 4.1

> Workflow of jobs and pipelines
> Understand Jenkins File
> Create and automate build pipelines
> Multibranch pipelines
> Explore Blue Ocean

> Workflow of release engineering
> Legacy pipelines model with Jenkins Jobs
> New Pipeline model using Jenkins Pipeline projects
> Features of the new pipeline model

> General release workflow
> - Commit to Git Repository
> - Checkout code from repository
> - Running test
> - Create a package
> - Deploy code

::It’s very easy understand this!!::

> Pipelines:
> Group of releases tasks
> Triggered in a specific sequence
> Isolation of unrelated tasks
> Identify problems
> Debugging
> Automation

> Legacy Pipeline Model
> Job 1: Checkout Code
> Job 2, 3, 4 and 5: Running different suite tests (parallel jobs)
> Job 6: Merge tag
> Job 7: Deploy Code

> New Pipeline Model: Uses stage indeed of Jobs

> New Features:
> - Requires a new set of plugins : Pipeline Plugins
> - Multi branch
> - Jenkins File configuration
> - Script with Groovy syntax (DSL)
> - Pipeline file could be in the project root

### Section 4.2 Jenkins File

> JenkinsFile syntax
> Exploring pipeline syntax
> Important definitions

> Jenkins file `jenkins_pipeline1`
```
pipeline{
agent{
	node{
		label 'docker-jenkins'
	}
}
options{
	timestamps()
}
environment{
	CC = 'clang'
}
}
```

> Jenkins file `jenkins_pipeline2`
```
pipeline{
	stages{
		stage('checkout'){
			steps {
				checkout scm
			}
		}
		stages('Build){
			parallel{
				stage('First Test'){
					steps {
						echo "Run first test here"
					}
				}
			}
		}
	}
}
```

Jenkins file `jenkins_pipeline3`

```
pipeline{
	post{
		always{
			cleanWs notFailBuild: true
		}
	}
}
```

All this three pipelines files are in the same project.

> Create a new job project as a pipeline
> See the pipeline syntax

### Section 4.3 Create and automate build pipelines

> Create a pipeline from JenkinsFile in the pipeline project
> Create a pipeline from JenkinsFile in the project project
> Exploring the pipeline build information
> Automating pipeline creating using scripts

Go to the python-project and create `python-project`
> Add a snippet with
> - Pipeline
> - Agent (docker Jenkins as label)
> - Options (timestamps)
> - Add stages: stage checkout, stage for build…

Jenkins UI
> Create a new project `test-project` (pipeline project)
> Go to advance options and create a Pipeline Script (groovy sandbox)
> Build now and you will see the logs of your project
> You have to see the pipeline dashboard status

In project (website) add a `Jenkinsfile`
> add the same script, and add a `post{ failure{ mail bcc:’’, …}}` at the end of the Jenkins file.

> Reconfigure our reciente pipeline project and choose `Pipeline script from SCM` (in Advanced Project Options)
> Add the repo with the project
> And built and check the pipeline status

> Create your pipeline with shell script, download the repository and start every stage with Jenkins cli jar and this would be attached by the UI Jenkins

### Section 4.4 Multibranch pipelines

In this section:
> Understanding multi branch pipelines
> Configuring prerequisites
> Creating and exploring a new pipeline

> In the repo `website`create a new branch named `develop`
> Add credentials for the github account
> Create a multi branch project
> Add in Branch Sources the GitHub credentials
> Built Configuration: by jenkinsfile
> This are going to check the repository and branches and check the `Jenkinsfile`
> With this, you will see two jobs for every branch

> Create a new branch and add by `Scan Repository now`
> Every branch has their own pipeline

### Section 4.5 Explore Ocean Blue

> Blue Ocean
> Install plugin
> Enable UI in Jenkins Dashboard
> Configure Blue Ocean Interface

> Find `Blue Ocean` and install
> Then you will see a new option in the menu
> You can see a new cool and useful UI for Jenkins

> You can create a new pipeline with Blue Ocean
> And you will see the branches

> If you create a repository without JenkinsFile, you can configure with Blue Ocean UI

## Section 5: Integrate with external services
## Section 5.1: Connect with Github
> Diagram for show the feature branch workflow
> Generate a token
> Create credentials on Jenkins and create a Secret Token for the GitHub token
> Add GitHub server
> Install `Github Branch Source Plugin`
> Create a Jenkins File in the project for merge a PR
> Jenkins have a Environmental Variables
> Create develop branch
> Create a new MultiBranch Pipeline project for our project
> Set the GitHub credentials
> Create a new feature/branch
> You have to see this branch in Jenkins
> Create a new PR for this branch vs Develop
> In Jenkins you have to see Branches and PR
> If the PR pass the pipeline is merged to develop
>

## Section 5.2: Connect with Sonarqube
> You have to run Sonarqube in your server
> Generate a token for Sonarqube

In Jenkins
> Install Sonarqube Scanner Plugin
> Configure System and add New SonarQube Server
> Download and move SonarQube Scanner ( from SonarQube Plugins) (in Jenkins machine)

In project:
> Configure sonnarQue at JenkinsFile
> Build the project and see the SonarQue projects

## Section 5.3: Connect with Artifactory
> You have to install Artifactory Frog in your machine with the default configuration

In Jenkins
> install Artifactory Plugin
> Configuration Artifactory: Add Server (add your server)

In Jenkins Machine
> Install rubygems, ruby-devel, ruby gem-fyi, rpm-build
> Install gem fpm

In project:
> Configure Jenkinsfile for use Artifcatory
> Create a new directory `pkg_resources/upload.json` for set the resources

In Jenkins Machine:
> Add repository for RPM Artifact

## Section 5.4: Connect with Jira

> Install plugin
> Add in Jenkins Jira Server Settings
> Add token as credential
> Add in Jira Jenkins Integration Plugin
> Go to Jira/Jenkins Configuration for add the Configuration (url, username and token)
> In JenkinsFile add a new step for set the respond to Jira
> Create a new issue, and name the code a new branch.
> Jira are going to show the comment from Jenkins

## Section 5.5: Connect with Slack
> Install Slack Notification Plugin in Jenkins
> Install the Jenkins CI App in Slack Channel
> Set the configuration for Slack Plugin in Jenkins
> Configure in Jenkins/Configuration the Global Slack Notifier Settings
> You can configure the message through the JenkinsFile in your project

