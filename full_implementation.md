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

### Getting started
- Click on Install suggested plugins
- Wait for the Jenkins to Install suggested plugins
- Create First Admin User or Skip the step [If you want to use this Jenkins instance for future use-cases as well, better to create admin user]

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

### Create a Jenkins Pipeline

- GO to dashboard > new item > create job
- item name:- ultimate cicd > item type : pipeline
- pipeline defination : pipeline script from SCM
- SCM : git
- Repository URL : https://github.com/iam-pratap/End-to-End-CI-CD-Implementation.git
- Branch specifier : */main
- script path : java-maven-sonar-argocd-helm-k8s/spring-boot-app/JenkinsFile

 apply and save

 ## Install the Sonar Qube Scanner plugin in Jenkins:

   - Go to Manage Jenkins > Manage Plugins.
   - In the Available tab, search for "SonarQube Scanner".
   - Select the plugin and click the Install button.
   - Restart Jenkins after the plugin is installed.
   
<img width="1392" alt="Screenshot 2024-10-24 at 18 20 27 PM" src="https://github.com/user-attachments/assets/cac16383-f043-442b-b5b8-babdafd367af">

Wait for the Jenkins to be restarted.

### Configure a Sonar Server locally

```
apt install unzip
adduser sonarqube
sudo su sonarqube
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
unzip *
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start
```
Check the sonarqube status `./sonar.sh status`

Hurray !! Now you can access the `SonarQube Server` on `http://<ip-address>:9000` 

Login username-admin , password-admin and Update with new password

### Add Sonarqube credentials in Jenkins

- Go to sonarqube > my account > security > generate-tokens
- Go to jenkins > manage jenkins > system > global credential(unrestricted)
- new credentials > secret text
- scope - Global(jenkins, nodes, items, all child items etc)
- secret - fdc07fb48b543afe1cf2f492ea4c2eaf78b7ffad
- ID - sonarqube

restart jenkins

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

### Add DockerHub credentials in Jenkins

- Go to dockerhub > my account > account setting > personal access token
- Go to jenkins > manage jenkins > system > global credential(unrestricted)
- new credentials > username and password
- scope - Global(jenkins, nodes, items, all child items etc)
- username - pratap15
- password - <password> 
- ID - docker-cred

### Add GitHub credentials in Jenkins

- Go to github > setting > developer-setting > personal access token > tokens(classic)
- Go to jenkins > manage jenkins > system > global credential(unrestricted)
- new credentials > secret text
- scope - Global(jenkins, nodes, items, all child items etc)
- secret - <secret-text>
- ID - github

Once you are done with the above steps, it is better to restart Jenkins.

Note: Create a new instance for kubernetes and ArgoCD

### Install and Configure Kubernetes

#### Update the Server

```
sudo apt update -y
```
#### Install docker and set the permissions

```
sudo apt install docker.io -y
```
```
sudo usermod -aG docker $USER && newgrp docker
```

#### Install minikube
```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```
```
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```
```
minikube start
```
#### Install Kubectl

```
sudo curl -LO https://dl.k8s.io/release/v1.30.0/bin/linux/amd64/kubectl
```
```
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
```

chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
```

```
kubectl version --client
```
#### Install ArgoCD on Kubernetes

Referance https://operatorhub.io/operator/argocd-operator

1. Install Operator Lifecycle Manager (OLM), a tool to help manage the Operators running on your cluster
```
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.29.0/install.sh | bash -s v0.29.0
```

2. Install the operator by running the following command:
```
kubectl create -f https://operatorhub.io/install/argocd-operator.yaml
```
3. After install, watch your operator come up using next command.
```
kubectl get csv -n operators
```

The following example shows the most minimal valid manifest to create a new Argo CD cluster with the default configuration.

vi argocd-basic.yml
```
apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
  labels:
    example: basic
spec: {}
```
Apply this configuration using `kubectl apply -f argocd-basic.yml`

check if all services are came up or not `kubectl get svc`

Change this service example-argocd-repo-server from ClusterIP to NodePort
```
kubectl edit svc example-argocd-repo-server
```
#### Forward port

```
kubectl port-forward --address 0.0.0.0 svc/example-argocd-server 30453:80
```
Go to browser and paste this `public-IP:30453`

<img width="1392" alt="Screenshot 2024-10-25 at 15 47 40 PM" src="https://github.com/user-attachments/assets/910a17a9-abb1-40f0-bdf2-ab5935070e35">

Paste the username and password

username - admin
password

Go to the terminal and do `kubectl get secret`

```
kubectl edit secret example-argocd-cluster
```
Now you will get the administrator password but you need to change this in base64

```
echo <Administrator-password> | base64 -d
```
Now you will get the argocd password

LoggedIn

- Create application
- Application name : test
- Project name : default
- Sync policy : Automatic
- Repository URL : https://github.com/iam-pratap/End-to-End-CI-CD-Implementation.git
- path : java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/

<img width="1392" alt="Screenshot 2024-10-25 at 15 10 02 PM" src="https://github.com/user-attachments/assets/00a9ff93-5841-412e-b2d7-3227a3496b87">

