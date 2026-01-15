===================================================================================

Deploying application to tomcat webserver

tomcat is opensource webserver
(Can be used for demo to clients) 
Java, Java Servlet pages can be deployed using tomcat

Considered an application: https://github.com/BONTHA-SREEVIDHYA/Tomcat.git
On an ec2 instance, installed java using war package on a different port: 8082 as tomcat by default has 8080 port 
```bash
sudo apt-get update
sudo apt install -y openjdk-17-jdk-headless
wget https://get.jenkins.io/war-stable/latest/jenkins.war
java -jar jenkins.war --httpPort=8082 ( open the port in security group of ec2 )
```
Access the jenkins @ <public-ip > : 8080

In Jenkins GUI: 

Install Maven ( in tools & configure maven3)

Install Stageview & deploy to container plugins 

Install tomcat on your ec2 

Take a duplicate tab as jenkins is actively running in one tab (mobaXterm)
cd /opt
sudo wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.85/bin/apache-tomcat-9.0.85.tar.gz
sudo tar -xvf apache-tomcat-9.0.85.tar.gz
<img width="1535" height="786" alt="image" src="https://github.com/user-attachments/assets/4e3aff60-e628-441d-b692-d0243587581a" />
Add tomcat credentials in Jenkins Credentials 

Configure the pipeline 

```groovy
pipeline {
    agent any
    tools{
        maven 'maven3'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/BONTHA-SREEVIDHYA/Tomcat.git'
            }
        }
        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('package') {
            steps {
                sh 'mvn clean package'
            }
        }
         stage('Deploy') {
            steps {
                deploy adapters: [tomcat9(alternativeDeploymentContext: '', credentialsId: '72ebabf2-0a4c-4d4d-84b7-0fe0662c6b0b', path: '', url: 'http://3.238.98.5:8080/')], contextPath: 'Planview', war: 'target/*.war'
            }
        }
    }
}

```
Access the tomcat server at <public ip>:8080/
<public ip>:8080/Planview ( as configured tomcat )

Build the pipeline 
<img width="1454" height="636" alt="image" src="https://github.com/user-attachments/assets/9249ed9e-ab7c-46f4-848a-1a6c656335ea" />
<img width="1887" height="1018" alt="image" src="https://github.com/user-attachments/assets/c58defc5-3888-4d5a-8acb-b3f569deb529" />








