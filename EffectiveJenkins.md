# Effective Jenkins
## Section 1 Testing
### Section 1.1 Introduction to Agile Testing

Agile Testing Quadrants

                                           Business Facing
					 Q2                   Q3
Support Programming ————————————— Critique Product
					  Q1                    Q4
					   Technology Facing

Q1: Unit, components, and integration test. AUTOMATED
Q2: Functional test, examples, story test, prototypes and simulation AUTOMATED AND MANUAL
Q3: Exploring test, scenarios, usability testing, UAT, A/B testing. MANUAL
Q4: Performance testing, load-security, scalability and reliability testing. TOOLS

### Section 1.2 Agile tests in the Continue Delivery World

Continues Delivery <- Testing is essential
- Code
- Build
- Test
- Release
- Deploy

Feedback loop
- Fast
- Reliable
- Isolate Failures

Automated Testing Pyramid

- Automated e2e test
- Automated API test
- Automated Integration Test
- Automation Component Test
- Automated Unit Test

Unit Test
Advantages
- Fast
- Reliable
- Isolate Failures
Disadvantages
- Don’t simulate a real user
- Don’t know if it works integrated with other component

Integration Test
Advantages
- Test connectivity
- Integration with other systems
Disadvantages
- Not most precisy
- Can be flaky

End to end test
Advantages
- Test a feature end to end
- Simulate the user interaction
Disadvantages
- Slow
- Fragile
- Expensive

### Section 1.3 Running Integration Test

Create a simple application with Spring Boot, java and gradle.
You have to install Jenkins.

- Create a simple pipeline project.
- Pipeline script from SCM (git), and Jenkins file path.
- Set the repository url
- We are going to create a Jenkinsfile

> problems using vagrant… installing Jenkins, and manage the Jenkins requirements (gradle)

- Creating the JenkinsFile and commit in the same repository
```
pipeline {
	agent { docker 'gradle:4.5-jdk8-alpine' }
	stages {
		stage ('Checkout') {
			steps {
				git 'here your repo url'
			}
		}
		stage('Build'){
			steps{
				sh 'gradle clean compileJava'
			}
		}
		stage('Unit-test'){
			steps {
				sh 'gradle test'
			}
		}
		stage('Integration-test'){
			steps {
				sh 'gradle integrationTest'
			}
		}
	}
	post {
		always {
			junit 'build/test-results/**/TEST-*.xml'
		}
	}
}
```

- Commit and push, and build now the pipeline
- See the console output for see the Jenkins file
- We could see the pipeline dashboard
- Commit and push and execute again.

### Section 1.4 Add a quality gate to the build pipeline

> Jacoco Test and Jenkins Quality

- Jacoco is for test coverage
- Add jacoco dependency in gradle and build again (using the previous project)
```
apply plugin: "jacoco"

jacocoTestReport {
  reports {
    html.enabled = true
    xml.enabled = true
    csv.enabled = true
  }
}
```

- Find reports in `build/reports/jacoco/test`

Adding Plugin
- Find jacoco plugin in Jenkins and install
- Go to the project, and configure the pipeline syntax
- Add pipeline syntax
	- Select sample step `jacoco coverage report`
	- Check `change build status according the thresholds`
	- Add 50% to line coverage (sunny)
- Add new step behind `integration test`
```
	stage('Code coverage') {
		steps {
			jacoco changeBuildStatus: true, maximumLineCoverage:'50'
		}
	}
```

- Commit and push and build
- See what happens!!

## Section 2 Database migrations

### Section 2.1 Understanding database migration
- Manage with scripts
- CI can manage this scripts and integrate
- Data schema evolve together with project code
- Versioning the database
- Stored in the version control

### Section 2.2 Introduction to Flyway

- Flyway is a version control for your database
- How does it work? Have a schema version for store all the versions
- Based on plain old sql or java migrations
- Naming Convention: `prefix + version + Separator (_) + Description + suffix (.sql)`
- Classpath: `db/migration/`
- How does it work? By command line, by java API, by grade task
- Support: Oracle, postgreSQL, H2, Mysql, SQL Server, etc…
- Recreate a database across the time

### Section 2.3 Running migrations on Integration tests

> Running migrations with H2 Database
> Prepare flyway

- H2 is a fast JDBC API
- We are going to use other project
- Using Spring Boot, and JPA
- Explain a lot of concepts about spring boot and JPA
- Create the first database script
- Using Test Properties in Spring Boot
- See the repositories.. etc
- On `build.gradle`
	- Add the flyway plugin `org.flywaydb.flyway`
	- Add the dependence for `compile("com.h2database:h2")`
	- Add flyway dependency `compile("org.flywaydb:flyway-core:5.0.7")`
	- Add at the end of the `build.gradle`file
```
flyway{
	//integration database test
	url = "jdbc:h2:./database/user_mgt;AUTO_SERVER=TRUE"
	user = "sa"
	password = "sa"
}
```
-  `gradle clean build`
- go to `database`folder
- The flyway save in a table the scripts created for flyway
- If we see the database we can see the test inserts
- We can configure a production database
- We can add a grade task for flyway stages

### Section 2.4 Running Migrations on CI

- Staging environment
	- Jenkins master
	- Jenkins node 1
	- App server (mysql database)

- CI Pipeline
	- Checkout the project
	- Build
		- build the package
	- Staging
		- Migrating deploy
		- Deploy package


- Use vagrant for create the machines
- Use shell script for configure and install dependencies at the three machines
- Create a Jenkins File with this stages
	- Checklout
	- Build
	- Migration
	- Deploy

> agent
> steps
> unstash
> archiveArtifacts

- Run pipeline 11:50 - 15:10 (dead time)
-  Rectify in mysql that the migration was correct

### Section 2.5 Changing the database

> Add new column to table user
> Run migration and integration testes
> Run migration on Staging environments

- Add new script for migrate the database  (add new column/attribute in spring)
- Modify the integration test
- Build the project with `gradle`
- Test the H2 connection
- After the build project, the H2 connection should have the migration done
- Commit and build the pipeline 8:30 - 10:57
- Check the changes in the databases H2 and mysql

## Section 3

### Section 3.1 What is branching strategy

> Branching strategy
> Workflow

- Introduction
> **Branching strategy or workflow** is a way  to define how new changes will be introduced to the mainline of the software from the version control point of view.

- The workflow should be simple
- Enable members of a team to develop a change in separated line
- Cheap on git
- Short lived branches
- Controversial CI Practice

- Centralized workflow
	- Multiple developers push to a shared repository
	- Great for transition from SVN or other centralized version control system
	- It doesn’t require any other branches, only the master will be used
		- Update the local repository
		- Code, test
		- Push on the master branch

### Section 3.2 Introduction to feature branch workflow

- Work on a particular change without annoying the master branch
- All changes should be implemented in a dedicated branch instead of the master branch
	- Update repository
	- Create a feature branch  (naming convention)
	- Code, test and repat
	- Push the branch
	- CI will execute a build pipeline in this feature-branch
	- Peers code review the changes
	- Finally merge to master branch

### Section 3.3 Working with feature branches in git

> Create feature branch
> Configure Jenkins to build feature branches

- Create a new repository on Github
- In this repository create a cradle project `gradle init --type java-library`
- Ignore `.gradle` and `build` folder
- Create a Jenkins File
```
pipeline {
	agent any
	stages {
		stage('Build'){}
		stage('deploy to staging'){}
		stage('deploy to production'){}
	}
}
```
- Commit and push to master

Jenkins Server
- Create a `multibranch pipeline`
- Branch Sources
	- |> Git  |> Add url in Project Repository
	- |> Behaviors |> Add |> Filter by name (regular expression) : `feature/*`
- Build configuration by Jenkins File
- Scan Multibranch selected option by 1 minute

The Project
- Create a new branch: `feature/my_feature_1`
- Add commits and push the branch
-  Create a new branch: `feature/my_feature_2`
- Add commits and push the second branch

In Jenkins
- You have to see the automatic branch scan
- For every branch you will run the pipeline
- Every branch would have his own pipeline results
- If you delete the branch in your git repository, also you are erase the pipeline in this feature

### Section 3.4 Configuring Jenkins for Multibranch Pipeline

In Jenkins
- Configure the project
- Change the Branch Source, and remove the discover branch by regular expression in name

In Project
- Modify the Jenkins file
```
stage("deploy to production"){
	when {
		branch "master"
	}
	steps {
		echo "deploying to production"
	}
}
```
- Commit and push (over the second branch)

Jenkins
- You can see build the pipeline in the all branches (include master)
- The stage “deploy to production” only will be build over the features branch

In the project
- Merge feature/2 into master without fast-forward
- Push master

## Section 4 Automating Pull Request builds

### Section 4.1 Setting up Jenkins on cloud

- Integrate Jenkins with github
	- Push changes on a branch
	- Trigger a new build
	- Post back the result of thew build
	- Receive feedback

Vagrant Machine
- Install plugin digital ocean in Vagrant `vagrant plugin install vagrant-digitalocean`
- Vagrant provider configuration (add Api key and token)
- `vagrant up --provider=digital_ocean`

Digital Ocean
- Get an account
- Go to Droplets for see the `jenkins-machine`
- Enter to the machine for configure the Jenkins

Jenkins Installation
- Set the admin password
- Install plugins 10:40 - 13:47
- Setup the admin account

### Section 4.2 Automating Pull-request verification with Github Organization

No video in drive

### Section 4.3 Setting up a GitHub web hook in Jenkins

Jenkins
- Configure Jenkins
- Go to Github
- Select `Manage hooks`
- Test connection (you will see the GitHub webhoook)

In Github
- Go to your repository
- Go to settings
- Go to integration and services
	- Add Jenkins plugin  (paste the GitHub web hook)

### Section 4.4 Automating Pull Request Verification with the pipeline project

In Jenkins
- Install Github Pull Request Builder
- Create a new `pipeline project`
- Select `GitHub project`and set the repo url
-  Select `Github pull request builder`
- Set GitHub API credentials `Anonymous connection`
- Configure the pipeline from SVM git and set the url again

- Go to Manage Jenkins
- Configure System
- Go to Github Pull Request builder
	- Store a personal token ( please get in the GitHub account)
	- And set the description

- Change anonymous connection for the last token connection credential)
- Select  `use github hooks for build triggering`
- Set the pipeline advance `Refspec`

- Make a simple pull request to this repo in GitHub and you could see the pipeline progress in Github refer to Jenkins
