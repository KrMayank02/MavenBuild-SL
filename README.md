#   Course-End Project: GitHub Actions Insured Assurance

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

sudo su –

apt update

apt install tomcat9 tomcat9-admin -y


Update user information for tomcat:

vim /etc/tomcat9/tomcat-users.xml

Scroll to the bottom of the file and just above the end tag </tomcat-users>

paste the below line:

<user username="tomcat" password="password" roles="admin-gui,manager-gui,manager-script"/>

There should not be any space before the line.

Screenshot:

<img width="895" height="581" alt="image" src="https://github.com/user-attachments/assets/ccf33a48-1b64-4667-bf0e-13929b680e35" />

Save the file (:wq!)

Open the server.xml file and change the connector port of tomcat

        vim /etc/tomcat9/server.xml
        
Scroll down to the Connector port tag as shown below and change port number to 9090.

Screenshot: 

<img width="852" height="556" alt="image" src="https://github.com/user-attachments/assets/b19278e5-8697-4f21-a469-f1901a142f91" />

Save the file:
:wq!

Open this script
 vim /usr/libexec/tomcat9/tomcat-locate-java.sh

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

systemctl restart tomcat9

To check tomcat is up and running

Go to browser and type localhost:9090

<img width="965" height="322" alt="image" src="https://github.com/user-attachments/assets/a2b9123f-5031-4910-98db-a76e725eb21e" />


Go to Browser and give http://localhost:8080 

This will launch Jenkins

Username: admin

Password: Root123$

You will be on Jenkins dashboard 

Manage Jenkins → Plugins -> Updates -> click on top most checkbox -> click on update

<img width="943" height="261" alt="image" src="https://github.com/user-attachments/assets/ee34f520-fc3a-4956-be5d-235c1891afdf" />

Click on Available plugins -> search for Deploy to container plugin → click on check box and click on install

<img width="955" height="240" alt="image" src="https://github.com/user-attachments/assets/4a962591-a157-43ce-a279-f14080d16f08" />


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







