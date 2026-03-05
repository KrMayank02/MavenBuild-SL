# Design a GitHub Actions workflow - CI/CD pipeline to invoke a Jenkins job for App Deployment

**Objective:** To create a GitHub Actions CI/CD pipeline workflow for invoking the deployment of a Java application as a Jenkins job using Tomcat Apache.

**Real-time Scenario:** Insured Assurance, a leading global insurance provider based in the US, offers a range of products including home, health, car, and life insurance.
The company is transitioning to a DevOps architecture and aims to automate code builds and deployments across various environments. To meet this
need, it has adopted GitHub Actions for code checkout, building, and testing automation and Jenkins for continuous deployment.
As a DevOps engineer at Insured Assurance, you are tasked with implementing a CI/CD pipeline using GitHub Actions and Jenkins.

-----------------------------------------------------------------------------------------------------------------------------------------------

## Tools, Server Used in This Project:

- Jenkins -> for continuous Deployment job
- Maven -> for continuous build
- Tomcat Apache -> Webserver
- Github Actions -> for creating CI/CD workflow

-----------------------------------------------------------------------------------------------------------------------------

## High Level Tasks/Steps:

The following tasks outline the process of implementing CI/CD using GitHub Actions and Jenkins:

1. Create a code repository on GitHub
   
2. Create a GitHub Actions pipeline to perform continuous integration
 
3. Configure Tomcat Apache for automated code deployment
 
4. Integrate the GitHub Actions pipeline to invoke the Jenkins pipeline
 
5. Invoke pipeline to validate automated deployment

----------------------------------------------------------------------------------------------------------------


## Output Result Screenshots:

**Go to Devops Lab and setup Jenkins and tomcat:**

Install Tomcat Server on the lab

Open the terminal on the lab, run below commands:

sudo su –

apt update

apt install tomcat9 tomcat9-admin -y


**Update user information for tomcat:**

vim /etc/tomcat9/tomcat-users.xml

Scroll to the bottom of the file and just above the end tag </tomcat-users>

paste the below line:

<user username="tomcat" password="password" roles="admin-gui,manager-gui,manager-script"/>

There should not be any space before the line.

-----------------------------------------------------------------------------------------------------------------------------------

<img width="895" height="581" alt="image" src="https://github.com/user-attachments/assets/ccf33a48-1b64-4667-bf0e-13929b680e35" />

-------------------------------------------------------------------------------------------------------------------------------------

Save the file (:wq!)

Open the server.xml file and change the connector port of tomcat

        vim /etc/tomcat9/server.xml
        
Scroll down to the Connector port tag as shown below and change port number to 9090.

-------------------------------------------------------------------------------------------------------------------------------------

<img width="852" height="556" alt="image" src="https://github.com/user-attachments/assets/b19278e5-8697-4f21-a469-f1901a142f91" />

------------------------------------------------------------------------------------------------------------------------------------

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

---------------------------------------------------------------------------------------------------------

Save the file.

Restart tomcat

systemctl restart tomcat9

To check tomcat is up and running

Go to browser and type localhost:9090

---------------------------------------------------------------------------------------------------------------------------

<img width="965" height="322" alt="image" src="https://github.com/user-attachments/assets/a2b9123f-5031-4910-98db-a76e725eb21e" />

------------------------------------------------------------------------------------------------------------------------------


Go to Browser and enter http://localhost:8080 

This will launch Jenkins

Username: admin

Password: Root123$

You will be on Jenkins dashboard 

Manage Jenkins → Plugins -> Updates -> click on top most checkbox -> click on update

--------------------------------------------------------------------------------------------------------------------------

<img width="943" height="261" alt="image" src="https://github.com/user-attachments/assets/ee34f520-fc3a-4956-be5d-235c1891afdf" />

--------------------------------------------------------------------------------------------------------------------------

Click on Available plugins -> search for Deploy to container plugin → click on check box and click on install

--------------------------------------------------------------------------------------------------------------------------

<img width="955" height="240" alt="image" src="https://github.com/user-attachments/assets/4a962591-a157-43ce-a279-f14080d16f08" />

--------------------------------------------------------------------------------------------------------------------------


In the browser terminal → enter: localhost:8080/restart 

Press enter.

Click on the yes button

Jenkins will be restarted, login again

Username: admin

Password: Root123$

---------------------------------------------------------------------------------------------------------------------------

Go to manage jenkins → tools → scroll to bottom→ maven→ click on add maven→ give name as mymaven → save the page

ADD tomcat credentials in Jenkins:

Manage jenkins → Credentials → Click on global → click on add credentials -> add below details → click on create button

Give username: tomcat 

Give password: password

Give id: tomcatid

Give description: tomcatid

---------------------------------------------------------------------------------------------------------------------

<img width="931" height="293" alt="image" src="https://github.com/user-attachments/assets/1cbe5403-b0b2-46e5-a6c0-11e47067e93d" />

-------------------------------------------------------------------------------------------------------------------------

<img width="923" height="290" alt="image" src="https://github.com/user-attachments/assets/c2fa14c9-3fb8-4741-a7b7-206972a7526b" />

-------------------------------------------------------------------------------------------------------------------------

Jenkins tomcat deployment pipeline

Create a new job → Give name as deployment-pipeline 

select pipeline project → press ok → 

click on pipeline tab → give below code:

------------------------------------------------------------------------------------------------------

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

-----------------------------------------------------------------------------------------------------------

Save and run it

Click on stages to see the execution. The Job executed successfully.

Add a trigger on the Jenkins job, so that it can run from another script. 

Go to your deployment Job → click on configure → go to trigger section -> select the last trigger→ give token as token1

-----------------------------------------------------------------------------------------------------------------------------

<img width="915" height="299" alt="image" src="https://github.com/user-attachments/assets/6a5ad985-84ee-4ebe-b089-fbb5f60ded13" />

-------------------------------------------------------------------------------------------------------------------------------

Copy this URL:

 JENKINS_URL/job/deployment-pipeline/build?token=TOKEN_NAME

Add to this URL:

http://localhost:8080/job/deployment-pipeline/build?token=token1

Now the job is ready to be triggered from a terminal or from a remote script:

Go to the lab terminal execute the below command

curl -l -u admin:Root123$ http://localhost:8080/job/deployment-pipeline/build?token=token1

Check that the pipeline job is executed on Jenkins.

Screenshot:

------------------------------------------------------------------------------------------------------------------------------------

<img width="925" height="460" alt="image" src="https://github.com/user-attachments/assets/e726beeb-1208-44c8-8bcd-309f55594695" />

--------------------------------------------------------------------------------------------------------------------------------------


GITHUB Action workflow:

=======================================

Fork the github repository:

After forking the repo→ click on Actions → click on  set up a workflow yourself 

Add below code and make changes for host and SSH command:

-------------------------------------------------------------------------------------------------------------

**github-actions-workflow.txt**

-------------------------------------------------------------------------------------------------------------


Commit the changes.

Screenshot:

----------------------------------------------------------------------------------------------------------------------


<img width="950" height="448" alt="image" src="https://github.com/user-attachments/assets/56e872ae-bfac-4c46-b752-6c7f39aae87e" />

------------------------------------------------------------------------------------------------------------------------


Click on Actions → see the Workflow execution:

All the stages of the Workflow executed successfully, pls see below 

------------------------------------------------------------------------------------------------------------------------

<img width="938" height="456" alt="image" src="https://github.com/user-attachments/assets/55d85a9c-9d39-4229-8f2b-e4599e8ded42" />

------------------------------------------------------------------------------------------------------------------------

Jenkins Pipeline job got triggered from GitHub Actions pipeline, pls see Console Screenshots:


<img width="913" height="543" alt="image" src="https://github.com/user-attachments/assets/716504da-e66a-4d94-a283-6370623d8714" />

------------------------------------------------------------------------------------------------------------------------


<img width="917" height="433" alt="image" src="https://github.com/user-attachments/assets/552e398b-4abc-4ac1-b672-720022e1ec1c" />

------------------------------------------------------------------------------------------------------------------------

War file deployed successfully on tomcat and the Application is running fine, pls see below screenshot:

<img width="954" height="472" alt="image" src="https://github.com/user-attachments/assets/94b3d1d5-ba2d-410b-9637-14ac600d3730" />

--------------------------------------------------------------------------------------------------------------------------------------

**Hence, The Project has been designed to implement a CI/CD Pipeline using GitHub Actions for Code Checkout, build, test and for invoking the deployment of a Java application as a Jenkins job using Tomcat Apache.**

