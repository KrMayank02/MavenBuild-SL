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
<img width="895" height="581" alt="image" src="https://github.com/user-attachments/assets/ccf33a48-1b64-4667-bf0e-13929b680e35" />
<img width="895" height="581" alt="image" src="https://github.com/user-attachments/assets/ccf33a48-1b64-4667-bf0e-13929b680e35" />
