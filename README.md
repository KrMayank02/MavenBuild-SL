   Course-End Project: GitHub Actions Insured Assurance

Objective: To create a GitHub Actions CI/CD pipeline workflow for invoking the deployment of a Java application as a Jenkins job using Tomcat Apache.

Tools, Server Used in This Project:
•	Jenkins -> for continuous Deployment job
•	Maven -> for continuous build
•	Tomcat Apache -> Webserver
•	Github Actions -> for creating CICD workflow


Project Implementation Steps:
JAVA Code repository on GitHub:
https://github.com/Sonal0409/DevOpsCodeDemo.git

Go to Devops Lab and setup Jenkins and tomcat:

Install Tomcat Server on the lab
Open the terminal on the lab, run below commands:
# sudo su –
# apt update
# apt install tomcat9 tomcat9-admin -y


Update user information for tomcat:

# vim /etc/tomcat9/tomcat-users.xml

Scroll to the bottom of the file and just above the end tag </tomcat-users>
paste the below line:
<user username="tomcat" password="password" roles="admin-gui,manager-gui,manager-script"/>
There should not be any space before the line.
Screenshot:
 

Save the file (:wq!)


Open the server.xml file and change the connector port of tomcat

        vim /etc/tomcat9/server.xml
Scroll down to the Connector port tag as shown below and change port number to 9090.
Screenshot: 

 

Save the file:
:wq!


Open this script
# vim /usr/libexec/tomcat9/tomcat-locate-java.sh

Update as below 
Updating the script this way adding the version 21 is solving the issue:
------------------------------------------------------------------------------------------------------
find_jdks() 
{ 
for java_version in 21 17 11 10 9 8 
do
------------------------------------------------------------------------------------------------

Save the file.

Restart tomcat

# systemctl restart tomcat9

To check tomcat is up and running

Go to browser and type localhost:9090

 
Go to Browser and give http://localhost:8080 

This will launch Jenkins

Username: admin
Password: Root123$

You will be on Jenkins dashboard 
Manage Jenkins → Plugins -> Updates -> click on top most checkbox -> click on update

 
Click on Available plugins -> search for Deploy to container plugin → click on check box and click on install

 

In the browser terminal → give: localhost:8080/restart 

Press enter.

Click on the yes button

Jenkins will be restarted, login again

Username: admin
Password: Root123$

===========
Go to manage jenkins → tools → scroll to bottom→ maven→ click on add maven→ give name as mymaven → save the page

ADD tomcat credentials in Jenkins:

Manage jenkins → Credentials → Click on global → click on add credentials -> add below details → click on create button

Give username: tomcat 
Give password: password
Give id: tomcatid 
Give description: tomcatid

 
 

Jenkins tomcat deployment pipeline

Create a new job →Give name as deployment-pipeline 
select pipeline project →press ok → 
click on pipeline tab → give below code:

----------------------------------------------------------------------------------------------------
pipeline{
    
    agent any
    
    tools{
        maven 'mymaven'
    }
    
    stages{
        stage('checkout code')
        {
            steps{
                git  'https://github.com/Sonal0409/DevOpsCodeDemo.git'
            }
        }
        
        stage('Build and Deploy the Code')
        {
            steps{
                sh 'mvn  package'
            }
            
            post{
                
                 success{
                    deploy adapters: [tomcat9(credentialsId: 'tomcatid', path: '', url: 'http://localhost:9090/')], contextPath: null, war: '**/*.war'
                }
                
            }
        }
        
    }
    }

-----------------------------------------------------------------------------------------------------
    


Save and run it
Click on stages to see the execution. The Job executed successfully.

Add a trigger on the Jenkins job, so that it can run from another script. 

Go to your deployment Job → click on configure → go to trigger section -> select the last trigger→ give token as token1

 

Copy this URL:

 JENKINS_URL/job/deployment-pipeline/build?token=TOKEN_NAME

Add to this URL:

http://localhost:8080/job/deployment-pipeline/build?token=token1


Now the job is ready to be triggered from a terminal or from a remote script:

Go to the lab terminal execute the below command

curl -l -u admin:Root123$ http://localhost:8080/job/deployment-pipeline/build?token=token1

Check that the pipeline job is executed on Jenkins.
Screenshot:
 



GITHUB Action workflow:
=======================================

Fork the repository:
https://github.com/ORG-05-June-2025-project01/MavenBuild-SL.git

 

After forking the repo→ click on Actions → click on  set up a workflow yourself 

Add below code and make changes for host and SSH command:
-------------------------------------------------------------------------------------------------------------
name: CI-CD using Maven and Tomcat
on:
 push:  # run when there is commit to repo
 workflow_dispatch:  # run manually
jobs:
 CI-CDjob:
   runs-on: ubuntu-latest
   steps:
    - name: Clone the repo on ubuntu server
      uses: actions/checkout@v4
    - name: Install Java and maven on ubuntu server
      uses: actions/setup-java@v4
      with:
       distribution: 'temurin'
       java-version: '11'
       cache: 'maven'
    - name: Build the code 
      run: mvn package
    - name: Connect to Lab machine and trigger a Jenkins job 
      uses: cross-the-world/ssh-scp-ssh-pipelines@latest
      with:
        host: '18.209.104.16'
        user: 'labuser'
        pass: 'Nuvelabs123$'
        port: 22
        connect_timeout: 10s
        first_ssh: |
          curl -l -u admin:Root123$ http://localhost:8080/job/deployment-pipeline/build?token=token1

-----------------------------------------------------------------------------------------------------

Commit the changes.

Screenshot:








 

Click on Actions → see the Workflow execution:
All the stages of the Workflow executed successfully, pls see below Screenshots:
 



 

 

 
 
 
 
Jenkins Pipeline job got triggered from GitHub Actions pipeline, pls see Console Screenshots:
 

 




 

War file deployed successfully on tomcat and the Application is running fine, pls see below screenshot:
 

Project gets completed here.
