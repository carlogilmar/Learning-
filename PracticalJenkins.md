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

