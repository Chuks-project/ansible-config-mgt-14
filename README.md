## EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP

Project 14 is a typical example of an End-to-End Implementtion of CI(Continous Integration) and CD(Continuous Delivery and Deployment) process. This project made use of Jenkins, Ansible, Artifactory, Sonarqube and PHP Application.

- Why do we need SonarQube?
SonarQube is an open-source platform developed by SonarSource for continuous inspection of code quality, it is used to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities.

- Why do we need Artifactory?
Artifactory is a product by JFrog that serves as a binary repository manager. The binary repository is a natural extension to the source code repository, in that the outcome of your build process is stored. It can be used for certain other automation, but we will it strictly to manage our build artifacts.

### SET UP
This project is partly a continuation of your Ansible work which we have seen from Project 11, so simply add and subtract based on the new setup in this project. It will require a lot of servers to simulate all the different environments from dev/ci all the way to production.

To get started, we will focus on these environments initially.
- Ci
- Dev
- Pentest

### Update the Ansible-Config-mgt repository by:
- Creating a folder called Deploy which contains the Ansible.cfg and Jenkinsfile
- Updating the inventory file by adding these files to it: ci.yml, pentest, Pre-prod, Prod
- Adding two more ROLES namely Artifactory and Sonarqube Roles

-  Next thing you have to do is to lauch and connect our Jenkins instance. And then install Jenkins but before that, you have to install git in order to clone the Repository. 

-Having installed git and cloned down the Ansible-config-mgt repository, you install Jenkins by:
    
    `sudo wget -O /etc/yum.repos.d/jenkins.repo \https://pkg.jenkins.io/redhat-stable/jenkins.repo`
    
    sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
    
    yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    `yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

- Install Java

`sudo yum install java-11-openjdk-devel -y`

- Open a Batch Profile

 vi .bash_profile

` 
  export JAVA_HOME=$(dirname= $(dirname $(readlink $(readlink$(which javac)))))
  export PATH=$PATH:$JAVA_HOME/bin 
  export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
`

- Reload bash profile

`source ~/.bash_profile`
 
 - Having installed Java and Epel release, next is to install Jenkins:

   `sudo yum install jenkins`
   
 - This is followd by: Starting, Enabling, View Jenkins status and also sudo systemctl daemon-reload

#### Set up Jenkins by creating the Admin user with the Password:
- Install Blue Ocean pluggin
- Create a new pipeline
- Create an Access Token

#### Create a Jenkinsfile and run Jenkinsfile configuration with Jenkins

- Jenkinsfile contains every step or jobs/actions that we want to execute. It is built in a code. 
- Every job in Jenkinsfile starts with a Pipeline followed by Agent. In the cousre of this project, we use 'Any Agent', followed by the Stages ad steps.

## Example
Add the code snippet below to start building the Jenkinsfile gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the shell script module to echo Building Stage. Go ahe

                                                      
  ![Jenkinsfile-Build stage  new](https://user-images.githubusercontent.com/65022146/214299598-36be0b65-df7c-49c9-98b9-8238382c86cc.png)
  
 
 - If this job is triggered in Jenkins(and in Blue Ocean) and the Build is successful, you will see an output thats looks like the screenshot below:


![branch echo-building stage](https://user-images.githubusercontent.com/65022146/214286301-38e8a344-bf82-4449-89fb-c35aa3b62787.png)


- At this point, another branch called 'feature/jenkinspipeline-stages'. This is because when you are working on a project, you do not want to be commiting directly to the main branch. it is always good to work on branch and only merge with the main branch when the code is good enough and have passedm the necessary test.

- Next, add another stage called 'Test', push the changes to github and try to trigger the build in Jenkins. Here, we are also going to add a quick task which include: Packages, Deploy ad Clean-up Stages--both Initial clean up of Jenkins directory and the eventual deletion of workspace and folders after jobs must have been built.
![Jenkinsfile-test-cleanup stage 1](https://user-images.githubusercontent.com/65022146/214308942-17771510-f5bc-4c56-bb50-e6a068084ec6.png)
     ![Jenkinsfile-test-cleanup stage 2](https://user-images.githubusercontent.com/65022146/214308946-dbbfd5ed-68cc-4b37-8e7a-db0257824ea9.png)
     
-  If the build is successful, you will see a screenshot like this:
![delete workspace successful](https://user-images.githubusercontent.com/65022146/214311188-d3be6a5c-0a27-4147-9873-8b4af62c7c04.png)



## RUNNING ANSIBLE PLAYBOOK FROM JENKINS
Now that you have a broad overview of a typical Jenkins pipeline. Let us get the actual Ansible deployment to work by:

- Installing Ansible on Jenkins
- Installing Ansible plugin in Jenkins UI
- Creating Jenkinsfile from scratch. (This time you have to delete all you currently have in Jenkinsfile and start all over to get Ansible to run successfully)

- The new Jenkinsfile will look like the screenshot below:

![Jenkinsfile for ansible plabk1](https://user-images.githubusercontent.com/65022146/214329229-fade2a13-0170-4c35-a110-b6986b95c0a8.png)
![Jenkinsfile for ansible plabk2](https://user-images.githubusercontent.com/65022146/214329237-a820abb4-c65e-4b79-974c-fe0f7b20b06d.png)



- Note that for Ansible to install MYSQL, there are some dependecies that needs to be installed for ansible to run properly

`
python3 -m pip install --upgrade setuptools
python3 -m pip install --upgrade pip
python3 -m pip install PyMySQL
python3 -m pip install mysql-connector-python
`

- For Postgresql Database, install

  `ansible-galaxy collection install community.postgresql`
 
 #### Here two more instances were added namely: Nginx(RHEL) and Database(Ubuntu)
 
#### Befoe running the ansible playbook, do not forget that ansible need to ssh into the instances. And because, ansible is not directly running the playbook, the private key of the instances has to be passed over to Jenkins by going to:

- Jenkins------>Manage Jenkins---->Manage Credentials

#### Also add ansible to Jenkins by navigating to:

 - Jenkins------>Manage Jenkins---->Global Tool Configuration

#### Note also that you can generate the ansible-playbook string by navigating to:

 - Jenkins--->Ansible-config-mgt(Repo name)---->Pipeline Syntax
 
  Now let us run an ansible-playbook against ci environment in the Jenkinsfile, Here you have to tweak the Playbook/site.yml by uncommenting the Nginx and Database roles and also in Mysql/defaults/main.yml, uncomment the Creating a database, tooling and the user, Webaccess
  
   
-  If the build is successful, you will see a screenshot like this:
        
    ![ansi inv ci succesful](https://user-images.githubusercontent.com/65022146/214336530-56c0905e-ad4a-4c8e-bf48-a9255b23714f.png)
    
    
    #### Parameterization
- The ansible-playbook above was ran under the ci env. However, Ansible can run in a different environment such as in Dev by using build with parameter
    
       ![Build with parameter](https://user-images.githubusercontent.com/65022146/214342717-d944acef-28b8-4919-8ca2-7b47f0700cb4.png)


- Now let's run an ansible playbook with Dev parameter. This will look like the image below if successful:

    ![ansible wt dev parameter successful](https://user-images.githubusercontent.com/65022146/214344183-52b8a197-912f-48d1-905d-725235fbcc17.png)
    
    
    
    ## CI/CD PIPELINE FOR TODO APPLICATION
    
    - Here you have to add PHP-Todo folder to your workspace.
    
 #### Phase 1 – Prepare Jenkins by:
   
   1. Forking and cloning down the PHP-Todo repository
   
   2. On you Jenkins server, install PHP, its dependencies and Composer tool as follows:
     
  
        `yum module reset php -y`
        
        `yum module enable php:remi-7.4 -y`
        
        `yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json`
        
        `systemctl start php-fpm`
        
        `systemctl enable php-fpm`
        
        
 - Install Composer
    
    ` curl -sS https://getcomposer.org/installer | php
     sudo mv composer.phar /usr/bin/composer`
        
        
    - Verify Composer is installed or not   
         
         `composer --version`
         
- Composer is used by PHP to install all the dependent libraries used by the application

         
         
#### Install Jenkins plugins
    
    - Plot plugin
    - Artifactory plugin

#### We will use plot plugin to display tests reports, and code coverage information.
#### The Artifactory plugin will be used to easily upload code artifacts into an Artifactory server. 

#### Launch artifactory instance

- Update the Inventory/ci with the artifactory private IP address
- Tweak the Playbook/site.yml by uncommenting the Artifactory role/assignment
- Copy the artifactory public IP address and access it on the browser in order to set up the Jfrog open source account
- Configure artifactory on Jenkins

#### Next, run the ansible playbook using the ci parameter to install the artifactory.

- If the playbook is successful, you will see an image like the screenshot below:

     ![artifactory ansi-playbook-ci succesful](https://user-images.githubusercontent.com/65022146/214430855-a94febce-f7c7-4a08-b5c1-5c946b8b98d2.png)
     
 
 
 
 - In Jenkins UI configure Artifactory  
 - Configure the server ID, URL and Credentials, run Test Connection.


#### Phase 2 – Integrate Artifactory repository with Jenkins

- Using Blue Ocean, create a multibranch Jenkins pipeline

- On the database server, create database and user: Here, navigate to Roles/Mysql/defaults/main.yml to tweak the configuration for mysql installation of Database and the user.

- php artisan uses the .env file to setup the required database objects – (After successful run of this step, login to the database, run show tables and you will see the tables being created for you)

- Also tweak the .env.sample
- Install mysql on Jenkin server
- Set the bind address 

   ![ansi mysql successful 1](https://user-images.githubusercontent.com/65022146/214438263-a15123f1-cc0f-4a32-94b1-87943681cf95.png)
   ![ansi mysql successful 2](https://user-images.githubusercontent.com/65022146/214438266-e99bbeea-944e-4439-ac37-2ccfb3eaa35a.png)
   
   
   #### Confirm the installation by logging into the database:
   
     ![connected to mysql via phptodo](https://user-images.githubusercontent.com/65022146/214441924-dc166023-034f-4bdd-910a-5e06603b8bbb.png)
     
     
     
     
    #### Update Jenkinsfile with proper pipeline configuration to include the following stages:
    
    - Initial cleanup
    - Checkout SCM
    - Prepare Dependencies
    - Execute Unit Tests


- If the playbook is successful, you will see an image that looks like the screenshot below:

     ![Execute unit test successful](https://user-images.githubusercontent.com/65022146/214448400-297e6a51-6fa7-4303-b38a-5ab16ef4c381.png)


#### Also add the following Stages to the Jenkinsfile:

- The code analysis step
- Plot Code Coverage Report



- If the build is successful, you will see an image that looks like the screenshot below:

    ![Plot code job successful](https://user-images.githubusercontent.com/65022146/214450921-d23eef8a-7b9a-46ba-a676-9ce86303c8f1.png)
    
    
 - Notice that after the build,you should be able find Plot on Jenkins as seen below:
 
     ![plots](https://user-images.githubusercontent.com/65022146/214543223-3fb5eefa-648e-4384-916f-fc180094bb1d.png)
     
     
 #### Update Jenkinsfile with two more stages called:
 
 - Package Artifact
 - Upload Artifact to Artifactory
 - Deploy to Dev Environment
 
 - However, to package the artifact you need to install Zip
 
 - Also tweak and replace the name of the Target artifactory before uploading the artifact to artifactory 
 
  - Now push the changes to Jenkins and build the job to package the artifact, Upload artifact and deploy to environment.
 
 - If successful, you will see an output that looks like the screenshot below:
 
     ![upload artifactory successfulpng png new](https://user-images.githubusercontent.com/65022146/214570450-874794b8-7691-4260-b937-b98b4764e737.png)

 
 - Notice that the php-todo artifact has been uploaded to the artifactory as seen below:
 
    
     ![Jfrog Artifactory-serving one artifact](https://user-images.githubusercontent.com/65022146/214572350-6bb0f034-78d4-45b9-a380-68c929b9fc71.png)

     
 
- Before we deploy to the Dev Environment, we have to create Php-Todo server
- Update the Dev.yml with the Todo private IP
- Tweak the Playbook/site.yml by uncommenting the Todo task
- Generate the password and the URL from the Jrog page and update the deployment.yml file 
     
     ![php deploy to dev env successful](https://user-images.githubusercontent.com/65022146/214569906-a107293f-5f83-4e1a-8e12-9c77bf3a8cd5.png)
      
 
 
 - Now that we have been able to build and deploy our php-Todo Application. The unit test and the code coverage test is not enough to say that our software is good generally. This is why Quality gate is introduced in the sense that if the code is bad or pass the quality gate, it won't be deployed to the Production environment. This is where Sonarqube comes to play.
    
    


### SONARQUBE INSTALLATION

Before we start getting hands on with SonarQube configuration, it is incredibly important to understand a few concepts:

- Software Quality – The degree to which a software component, system or process meets specified requirements based on user needs and expectations.
- Software Quality Gates – Quality gates are basically acceptance criteria which are usually presented as a set of predefined quality criteria that a software development project must meet in order to proceed from one stage of its lifecycle to the next one.

- SonarQube is a tool that can be used to create quality gates for software projects, and the ultimate goal is to be able to ship only quality software code.

Despite that DevOps CI/CD pipeline helps with fast software delivery, it is of the same importance to ensure the quality of such delivery. Hence, we will need SonarQube to set up Quality gates. In this project we will use predefined Quality Gates (also known as The Sonar Way). Software testers and developers would normally work with project leads and architects to create custom quality gates.

#### Step 1:

- Lauch Sonarqube instance
- Download an ansible role for Sonarqube
- Update the ci environment with the Sonarqube private IP address


#### Install Sonarqube through ansible playbook:
  
![sonarqube successful png2](https://user-images.githubusercontent.com/65022146/214584863-ad7c6074-3392-4f5e-a65f-1b8f99ae268a.png)
    
- Copy the Sonarqube public IP and access it on the browser as seen below:
    
![sonarqube code quality page png2](https://user-images.githubusercontent.com/65022146/214586797-ebdb790c-fa52-4a63-ad60-73fede5f4605.png)
 
![sonarqube successful](https://user-images.githubusercontent.com/65022146/214584868-48c16a07-e579-47b6-9ded-1808aee23164.png) 
     
- Install sonarqube pluggin on the Jenkins UI

- Configure Sonarqube and the Jenkins for Quality gate


- Generate authentication token in SonarQube:

   ![sonarQube-token generated](https://user-images.githubusercontent.com/65022146/214591634-e20f8b33-3c29-4ee9-aca2-8334333f8eb4.png)


- Configure Quality Gate Jenkins Webhook in SonarQube:

     ![jenkinurl-sonarqube-webhooks-created](https://user-images.githubusercontent.com/65022146/214593341-e6e55ff8-3a44-42bc-8ca0-ed85ba101f31.png)
     
     
     
- Setup SonarQube scanner from Jenkins – Global Tool Configuration  

- Update Jenkins Pipeline to include SonarQube scanning and Quality Gate:

   ![image](https://user-images.githubusercontent.com/65022146/214601418-f7bd2e2a-fec9-4da9-9499-5ad44037e8db.png)
   
   
- Notice that the quality gate was able to pass before deploying to the Dev environment. But this is not to say that it passed with some bugs.

- For a better understanding, let us navigate to php-todo project in SonarQube:
     
     ![sonarqube-php-todo successful](https://user-images.githubusercontent.com/65022146/214602557-cd2086a1-b13e-4257-9a1d-72a4ed2cb7c5.png)
     
- Notice from above, that there were 13 bugs.     
     
     
 - In the development environment, this is acceptable as developers will need to keep iterating over their code towards perfection. But as a DevOps engineer working on the pipeline, we must ensure that the quality gate step causes the pipeline to fail if the conditions for quality are not met.


### CONCLUSION
 
 We are going to conclude this project by:
 
- Introducing Jenkins agents/slaves – Add 2 more servers to be used as Jenkins slave. 

- Configure Jenkins to run its pipeline jobs randomly on any available slave nodes.

- Configure webhook between Jenkins and GitHub to automatically run the pipeline when there is a code push.

- Deploy the application to all the environments

#### SET UP

- Spin up an instance
- Install java
- Open a bash_profile 
- Configure Agent for Jenkins Slave on the Jenkin UI
- Configure Webhook for php-Todo 

   ![Webhook created](https://user-images.githubusercontent.com/65022146/214610262-11283111-108f-4557-8470-a9cd2d7dcdd0.png)
   ![Built in node](https://user-images.githubusercontent.com/65022146/214610271-3626cb0d-d545-4413-ac87-307ab4ab7c53.png)







