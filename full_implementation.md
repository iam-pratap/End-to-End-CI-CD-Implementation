# Prerequisites

## AWS EC2 

Go to AWS management console and Install EC2 instance(ubuntu-22.04)

Update the instance
```
sudo apt update -y
```

## Jenkins

Install Java(openjdk)
```
sudo apt install openjdk-17-jre -y
```

Verify java
```
java -version
```

Now, you can proceed with installing Jenkins
```
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
To check the status of Jenkins
```
sudo systemctl status jenkins
```
Go to browser and paste the public ip address `ip-address:8080`

Note: Edit the inbound traffic rule to only allow custom TCP port `8080`
  
After you login to Jenkins, 
      - Run the command to copy the Jenkins Admin Password - `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`
      - Enter the Administrator password
      
<img width="1291" alt="Screenshot 2023-02-01 at 10 56 25 AM" src="https://user-images.githubusercontent.com/43399466/215959008-3ebca431-1f14-4d81-9f12-6bb232bfbee3.png">

### Click on Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 58 40 AM" src="https://user-images.githubusercontent.com/43399466/215959294-047eadef-7e64-4795-bd3b-b1efb0375988.png">

Wait for the Jenkins to Install suggested plugins

<img width="1291" alt="Screenshot 2023-02-01 at 10 59 31 AM" src="https://user-images.githubusercontent.com/43399466/215959398-344b5721-28ec-47a5-8908-b698e435608d.png">

Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

<img width="990" alt="Screenshot 2024-10-24 at 10 21 43 AM" src="https://github.com/user-attachments/assets/3b2eae58-399b-4cf1-a682-73c75d66032a">

Jenkins Installation is Successful. You can now starting using the Jenkins 

<img width="990" alt="Screenshot 2024-10-24 at 10 25 41 AM" src="https://user-images.githubusercontent.com/43399466/215961440-3f13f82b-61a2-4117-88bc-0da265a67fa7.png">

## Install the Pipeline: Stage View in Jenkins:

   - Log in to Jenkins.
   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Pipeline: Stage View".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://github.com/user-attachments/assets/98360675-8c24-40ad-ab30-736dbf074540">

Wait for the Jenkins to be restarted.

## Install the Docker Pipeline plugin in Jenkins:

   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "Docker Pipeline".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2023-02-01 at 12 17 02 PM" src="https://user-images.githubusercontent.com/43399466/215973898-7c366525-15db-4876-bd71-49522ecb267d.png">

Wait for the Jenkins to be restarted.


## Docker Slave Configuration

Run the below command to Install Docker

```
sudo apt update
sudo apt install docker.io
```
 
### Grant Jenkins user and Ubuntu user permission to docker deamon.

```
sudo su - 
usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
```

Once you are done with the above steps, it is better to restart Jenkins.

```
http://<ec2-instance-public-ip>:8080/restart
```

The docker agent configuration is now successful.

### Create a Jenkins Pipeline

- GO to dashboard > new item > create job
- item name:- ultimate cicd > item type : pipeline
- pipeline defination : pipeline script from SCM
- SCM : git
- Repository URL : https://github.com/iam-pratap/End-to-End-CI-CD-Implementation-.git
- Branch specifier : */main
- script path : spring-boot-app/JenkinsFile

 apply and save

## Install the Sonar Qube Scanner plugin in Jenkins:

   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "SonarQube Scanner".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2024-10-24 at 18 20 27 PM" src="https://github.com/user-attachments/assets/cac16383-f043-442b-b5b8-babdafd367af">

Wait for the Jenkins to be restarted.
