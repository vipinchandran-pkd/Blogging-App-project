# **🚀 Blogging App DevOps Pipeline project in kubernetes**

## **Initial setup and Deployment**


### **Phase 1:Clone the repository**

```bash
git clone https://github.com/didin8080/Blogging-App.git
```
**Create a private Github Repository and push the code to the repository. Create a token don't enable delete repo and package.**


### **Phase 2:Create Security group(production)**

- Create a security group .Allow ports
    - SMTP 25
    - CUSTOM TCP 2000 - 11000
    - HTTP 80
    - HTTPS 443
    - SSH 22
    - CUSTOM TCP 6443
    - SMTPS 465
    - CUSTOM TCP 30000-32767
    - CUSTOM TCP 8081




### **Phase 3: Launch EC2 (ubuntu 22.04) for Sonarqube**

- Create an EC2 instance on AWS with Ubuntu 22.04, t3.medium, storage= 30gb.
- Attach the Security group with instance. (production) 
- Connect it using SSH.
- Change the hostname to sonar

**Step 1: Install Docker**

```bash
vi docker.sh
```
Paste it below

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```


```bash
sudo chmod +x docker.sh
sh docker.sh
```
Acess it using 

`publicIp:9000`

**Step 2: Install Sonarqube using docker**

```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

```bash
docker ps
docker images
```


### **Phase 4: Launch EC2 (ubuntu 22.04) for Nexus Repository**

- Create an EC2 instance on AWS with Ubuntu 22.04, t3.medium, storage= 30gb.
- Attach the Security group with instance. (production) 
- Connect it using SSH.
- Change the hostname to nexus

**Step 1: Install Docker**

```bash
vi docker.sh
```
Paste it below

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```


```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

```bash
sudo chmod +x docker.sh
sh docker.sh
```



**Step 2: Install Nexus using Docker**

```bash
docker run -d --name nexus -p 8081:8081 sonatype/nexus3
```

```bash
docker ps
docker images
```

Acess it using 

`publicIp:8081`



Default username is admin 

To get password. Go inside the container

```bash
docker exec -it <container-id> /bin/bash
```
Relace it with your container id

```bash
ls
```

```bash
cd sonatype-work
```

```bash
ls
```

```bash
cd nexus3
```

```bash
ls
```

```bash
cat admin.password
```

It will generate a password copy till bash

eg:**(c53ce18a-2a58-4026-8da0-4d00602df100dbash-4.4$)**   you will get like this copy till   **(c53ce18a-2a58-4026-8da0-4d00602df100d)**

Copy password and paste it on the nexus repository.

Change password.

Enable anonymous access

And exit from container

```bash
exit
```


### **Phase 5: Launch EC2 (ubuntu 22.04) for Jenkins**

- Create an EC2 instance on AWS with Ubuntu 22.04, t3.medium, storage= 30gb.
- Attach the Security group with instance. (production) 
- Connect it using SSH.
- Change the hostname to nexus

**Step 1: Update the package**

```bash
sudo apt-get update
```


**Step 2: Install Jenkins**

**Installing Jenkins**


```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```

**Installing Java**


```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version
```

**Start Jenkins**

```bash
sudo systemctl enable jenkins
```

```bash
sudo systemctl start jenkins
```

```bash
sudo systemctl status jenkins
```


Acces it with port number 8080

`<public-ip:8080>`


Setup Jenkins



**Step 3: Install Docker**

```bash
vi docker.sh
```
Inside this

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```


```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

```bash
sudo chmod +x docker.sh
sh docker.sh
```
```bash
sudo usermod -aG docker $USER
newgrp docker
sudo chmod 666 /var/run/docker.sock
```

Login into docker hub on browser 

Login docker in terminal using

```bash
docker login -u didin8080
```

It asks for a password provide dockerhub password

**Step 4:Install Trivy**

```bash
vi trivy.sh
```

Paste it below.

```bash
sudo apt-get install wget apt-transport-https gnupg
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb generic main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

```bash
sudo chmod +x trivy.sh
./trivy.sh
```

**Step 5:Install Kubectl**

```bash
vi kubectl.sh
```

paste it inside

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

```bash
sudo chmod +x kubectl.sh
sh kubectl.sh
```
### **Login to docker hub on Browser and Create a  Private Repository (bloggingapp)**


### **Phase 6:Launch EC2 (ubuntu 22.04) for Kubernetes**

- Create 3 EC2 instance on AWS with Ubuntu 22.04, t3.large, storage= 30gb.
- Attach the Security group with instance.(game) 
- Connect it using SSH.
- Change the hostname one as Master other as Slave-1 and Slave-2

**Connecting (All nodes)**

- Step 1: Update and Upgrade Ubuntu **(all nodes)**
```bash
sudo apt update && sudo apt upgrade -y
```

- Step 2: Disable Swap **(all nodes)**
```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```

- Step 3: Add Kernel Parameters **(all nodes)**

```bash
sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF
sudo modprobe overlay
sudo modprobe br_netfilter
```
- Step 4:Configure the critical kernel parameters for Kubernetes using the following:

```bash
sudo tee /etc/sysctl.d/kubernetes.conf <<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
```

- Step 5: Reload the change 

```bash
sudo sysctl --system
```

- Step 6: Install Containerd Runtime **(all nodes)**

```bash
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
```

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

```bash
sudo apt update
sudo apt install -y containerd.io
```

```bash
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
```

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

- Step 7: Add Apt Repository for Kubernetes **(all nodes)**

```bash
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
- Step 8: Install Kubectl, Kubeadm, and Kubelet **(all nodes)**

```bash
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

- Step 9: Initialize Kubernetes Cluster with Kubeadm **(master node)**

```bash
sudo kubeadm init
```

- Step 10: Run the following commands on the **(master node)**

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
one by one

```bash
kubectl get nodes
```

**In between if  some error occured type this command (All Nodes)**

```bash
sudo modprobe br_netfilter
echo 1 | sudo tee /proc/sys/net/bridge/bridge-nf-call-iptables
echo 1 | sudo tee /proc/sys/net/ipv4/ip_forward
```

- Step 11: Add Worker Nodes to the Cluster **(worker nodes)**

```bash
kubeadm join 138.197.184.45:6443 --token 72ww2b.6orffywqcf5s4p2z \
        --discovery-token-ca-cert-hash sha256:aafb79cdd45a6e3b3fac01fb3efba0817360b01f90a4b6c3f11567108a36ba67
```

**Replace it with your key that you have got on last of `sudo kubeadm init` command on masternode**


- Step 12: Install Kubernetes Network Plugin **(master node)**

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```


```bash
kubectl get pods -n kube-system
kubectl get nodes
```

The output should be 1 Masternode and 2 workernode both Status should be **ready**


### On Masternode terminal
 

Create a Docker Secret for Private repo 

```bash
kubectl create secret docker-registry registrycred --docker-server=https://index.docker.io/v1/ --docker-username=<your-docker-username> --docker-password=<your-password>
```


Create service.yml 

```bash
vi svc.yaml
```

paste it below

```bash
apiVersion: v1
kind: ServiceAccount
metadata: 
  name: jenkins
  namespace: webapps
```

Create namespace

```bash
kubectl create ns webapps
```

```bash
kubectl apply -f svc.yaml
```


Create role.yml

```bash
vi role.yaml
```

paste it below

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - secrets
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

```bash
kubectl apply -f role.yaml
```

Create bind.yaml

```bash
vi bind.yaml
```
paste it below

```bash
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins 
```

```bash
kubectl apply -f bind.yaml
```

create a API token

```bash
vi sec.yaml
```
paste it below


```bash
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins
```

```bash
kubectl apply -f sec.yaml -n webapps
```

```bash
ls
```

```bash
kubectl describe secret mysecretname -n webapps
```
Now you will get the token. Copy the token and add it on jenkins credentials.



### **Phase 7: Configure on Jenkins**

**Step 1: Install necessary plugins on Jenkins**

Goto Jenkins dashboard--> Manage Jenkins --> Plugins

Install below plugins
- Eclipse temurim installer
- sonarqube scanner
- Maven integration
- Config File Provider
- Pipeline Maven integration
- docker 1.6.2
- docker pipeline
- docker-build-step
- Kubernetes 
- Kubernetes CLI
- Kubernetes Credentials
- Kubernetes client API
- pipeline:stage view
- email extension template



**Step 2: Tools configuration in jenkins**

Goto Jenkins dashboard--> Manage Jenkins --> tools 
- add jdk,give name as jdk17 ,install automatically ,add installer, install from adoptium.net, select jdk-17.0.8.1+1 
- add sonarqube scanner--> name= sonar-scaner,insall automatically,
- add maven --> name= maven ,install automaticaly,version=3.6.1
- add docker --> name=docker,install automatically,add installer ,download from docker.com



**Step 3: ADD Credentials on Jenkins**

- On sonarqube -> admininstration-->security--> user--> rightside 3dots--> give any name --> generate token
- On jenkins --> manage jenkins-->credentials-->global--> add credentials--> kind=secret text --> on secret paste the token and give any name and description-->give any id and description-->create
- On jenkins --> manage jenkins-->credentials-->global--> add credentials-->kind=username with password--> username=give the username of dockerhub--> password=password of dockerhub-->create
- On jenkins --> manage jenkins-->credentials-->global--> add credentials--> kind=username with password--> username=give github username --> password= the toke that you created on github.
- On jenkins --> manage jenkins-->credentials-->global--> add credentials--> kind=username with password--> username= provide your mail id -->password= the APP PASSWORD that you created --> id smtp.
- On jenkins --> manage jenkins-->credentials-->global--> add credentials--> kind=secret text --> on secret paste the api token that created on master node for kubernetes --> id= k8s 


**Step 4: Create webhook in sonarqube**

- on jenkins--> manage jenkins--> system--> add sonarqube server -->give name as sonar-server--> on server url= paste the url of sonarqube with sonarqube-webhook eg:**(192.168.20.173:8080/sonarqube-webhook)** -->select the authentication --> apply and save .

**Step 5: System Configuration in Jenkins**

On jenkins--> manage jenkins--> system

- add sonarqube server -->give name as sonar-server--> on server url= paste the url of sonarqube -->select the authentication --> apply and save .
- Extended email notification --> On SMTP server= smtp.gmail.com --> SMTP port = 465 -->advanced --> enale use SSL --> on credentails = add credentials that we added for gmail
- E-mail Notification --> On SMTP server = smtp.gmail.com --> advanced --> enable use ssl --> smtp port 465--> enable use SMTP authentication --> username= didinpg8080@gmail.com --> on password = app password `eg:(drsn anne afrd pcfx)`--> enable test configuration by sending email --> enter our email--> test configuration --> Goto your email you will get an test email from jenkins --> apply and save


**Step 6: Configure Project on Sonarqube**

On Sonarqube--> Create project -->give projet name and key name --> and create
--> select locally --> generate a token --> continue --> other --> Linux --> copy the command and paste it on the sonarqube analysis portion on jenkins pipeline


**Step 7: on nexus repository**

- Goto nexus repository --> browsers --> copy the url of `maven-release`
- Goto github --> pom.xml --> edit that file --> on last --> Replace maven-release with new url
- Goto nexus repository --> browsers --> copy the url of `maven-snapshots`
- Goto github --> pom.xml --> edit that file --> on last --> Replace maven-snapshots with new url

**Step 8: Add Config file on Jenkins**

on jenkins--> manage jenkins--> Manages files --> Add a new config --> enable global maven settings.xml -->scrool down --> ID = global-settings -->      On Content -->scrool till mid you will see `<server>` --> on server    
`(-->)`remove from back and paste it on front .

eg: \
`<server>` \
`<id>deploymentid</id>` \
`<username>repouser</username>` \
`<password>repopwd</password>` \
`</server>` \
`-->`


it will be like this edit like this

`-->` \
`<server>` \
`<id>meven-release</id>` \
`<username>admin</username>`  # nexus repo username  
`<password>admin123</password>` # nexus repo password \
`</server>` 

`<server>` \
`<id>meven-snapshots</id>` \
`<username>admin</username>`  # nexus repo username  
`<password>admin123</password>` # nexus repo password \
`</server>`


**Step 9: Configure CI/CD pipeline in jenkins**

Before pasting the pipeline script, do the following changes in the script
1. In the stage 'Tag and Push to Doccd ~/.kube
    ls
    cat configkerHub', give your docker-hub username. Similar thing you should do in 'Deploy to kubernetes' stages
2. In post actions stage in pipeline, make sure to give the email id you have configured in jenkins.
3. Replace the checkout repo and generate the link using Pipeline Syntax
4. while generating the github link will will be in private.To get the link add credentials on the link 
5. while writing the pipeline on kubernetes deploying stage to get the serverip and cluster name go for on master node 

`withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.22.204:6443')`  To get the server ip and cluster name

```bash
cd ~/.kube
ls
cat config
```

**Pipeline**

```groovy
pipeline {
    agent any
    tools{
        jdk 'jdk17'
        maven 'maven'
    }
    environment{
      SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('git checkout') {
            steps {
               git branch: 'main', credentialsId: 'git', url: 'https://github.com/didin2003/Blogging-App.git'
            }
        }
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Trivy file scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
            }
        }
        stage('Sonarqube analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                sh """
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=BloggingApp \
                    -Dsonar.sources=. \
                    -Dsonar.java.binaries=target/classes \
                    -Dsonar.host.url=http://16.16.251.145:9000 \
                    -Dsonar.login=sqp_55e2328477212ad39a86b24286dc0a029032cc55
                """
               }
            }
        }
        stage('build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Publish artifacts') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
                 }
            }
        }
        stage('Build and Tag to dockerhub') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker') {
                        sh "docker build -t didin8080/blogging-app:latest ."
                    }
                }
            }
        }
        stage('Trivy image scan') {
            steps {
                sh "trivy image --format table -o image.html didin8080/blogging-app:latest"
            }
        }
        stage('Push to dockerhub') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker push didin8080/blogging-app:latest"
                    }
                }
            }
        }
        stage('k8-build') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.22.204:6443'){ 
                    sh "kubectl apply -f deployment-service.yml"
                    sleep 20
                }
            }
        }
        stage('k8-verify') {
            steps {
                withKubeConfig(caCertificate: '', clusterName: 'kubernetes', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://172.31.22.204:6443'){
                    sh "kubectl get pods"
                    sh "kubectl get svc"
                }
            }
        }
    }
     post {
    always {
        emailext attachLog: true,
            subject: "'${currentBuild.result}'",
            body: """
                <html>
                <body>
                    <div style="background-color: #FFA07A; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">Project: ${env.JOB_NAME}</p>
                    </div>
                    <div style="background-color: #90EE90; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">Build Number: ${env.BUILD_NUMBER}</p>
                    </div>
                    <div style="background-color: #87CEEB; padding: 10px; margin-bottom: 10px;">
                        <p style="color: white; font-weight: bold;">URL: ${env.BUILD_URL}</p>
                    </div>
                </body>
                </html>
            """,
            to: 'didinpg8080@gmail.com',
            mimeType: 'text/html',
            attachmentsPattern: 'trivy.txt'
        }
    }
}
```



### Build it


Your application is deployed in kubernetes to access it. 

Goto console output --> scroll down -->  you will get an port number under `kubectl get svc -n webapps`

Copy the ip adress of Slave-1 instance or Slave2 instance (workernode) and paste it in new tab with port number `eg(32349)` you will get some different port.

`<public-ip:32349>`

<div align="center">
<img src="img/login-page.png" alt="Logo" height="500%" width="100%" >
<p align="center"> Login page </p>
</div>


<div align="center">
<img src="img/home-page.png" alt="Logo" height="500%" width="100%" >
<p align="center"> Home page </p>
</div>


**Check the IP address both are on different IP one is Slave1 and another is Slave2**




## **Phase 8: Moniter the Application**

- Create an EC2 instance on AWS with Ubuntu 22.04, t3.medium, storage= 30gb.
- Attach the Security group with instance. (game) 
- Connect it using SSH.
- Change the hostname to moniter



**Step 1: Update the packages**

```bash
sudo apt-get update
```


**Step 2: Installing Prometheus**

```bash
sudo useradd --system --no-create-home --shell /bin/false prometheus
wget https://github.com/prometheus/prometheus/releases/download/v2.47.1/prometheus-2.47.1.linux-amd64.tar.gz
#Extract Prometheus files, move them, and create directories:
tar -xvf prometheus-2.47.1.linux-amd64.tar.gz
cd prometheus-2.47.1.linux-amd64/
sudo mkdir -p /data /etc/prometheus
sudo mv prometheus promtool /usr/local/bin/
sudo mv consoles/ console_libraries/ /etc/prometheus/
sudo mv prometheus.yml /etc/prometheus/prometheus.yml
#Set ownership for directories:
sudo chown -R prometheus:prometheus /etc/prometheus/ /data/
```

**Create a systemd unit configuration file for Prometheus:**

```bash
sudo vi /etc/systemd/system/prometheus.service
```

**Add the following content to the prometheus.service file:**

```plaintext
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=prometheus
Group=prometheus
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/data \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.enable-lifecycle

[Install]
WantedBy=multi-user.target
```

**Enable and start Prometheus:**

```bash
sudo systemctl enable prometheus
sudo systemctl start prometheus
```

**Verify Prometheus's status:**

```bash
sudo systemctl status prometheus
```

Press Control+c to come out



Access Prometheus in browser using your server's IP and port 9090:

`http://<your-server-ip>:9090`

Click on 'Status' dropdown ---> Click on `Targets` ---> You can see `Prometheus (1/1 up)`

**Step 3: Installing Node Exporter**

```bash
cd 
sudo useradd --system --no-create-home --shell /bin/false node_exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
#Extract Node Exporter files, move the binary, and clean up:
tar -xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo mv node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin/
rm -rf node_exporter*
```

**Create a systemd unit configuration file for Node Exporter:**

```bash
sudo vi /etc/systemd/system/prometheus.service
```

**Add the following content to the node_exporter.service file:**

```plaintext
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

StartLimitIntervalSec=500
StartLimitBurst=5

[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=on-failure
RestartSec=5s
ExecStart=/usr/local/bin/node_exporter --collector.logind

[Install]
WantedBy=multi-user.target
```


**Enable and start Node Exporter:**

```bash
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

**Verify Node Exporter status:**

```bash
sudo systemctl status node_exporter
```

Press Control+c to come out



**Step 4: Configure Prometheus Plugin Integration**

As of now we created Prometheus service, but we need to add a job in order to fetch the details by node exporter. So for that we need to create 2 jobs, one with 'node exporter' and the other with 'jenkins' as shown below;


Integrate Jenkins with Prometheus to monitor the CI/CD pipeline.

Prometheus Configuration:


**To configure Prometheus to scrape metrics from Node Exporter and Jenkins, you need to modify the prometheus.yml file.**

```bash
cd /etc/prometheus/
ls -l
#You can see the "prometheus.yml" file
sudo vi prometheus.yml
```

**You will see the content and also there is a default job called "Prometheus" Paste the below content at the end of the file;**

```yaml
- job_name: 'node_exporter'
    static_configs:
      - targets: ['<MonitoringVMip>:9100']

  - job_name: 'jenkins'
    metrics_path: '/prometheus'
    static_configs:
      - targets: ['<your-jenkins-ip>:<your-jenkins-port>']
```

In the above, replace <your-jenkins-ip> and <your-jenkins-port> with the appropriate IPs ----> esc ----> :wq


**Check the validity of the configuration file:**

```bash
promtool check config /etc/prometheus/prometheus.yml
```

You should see "SUCCESS" when you run the above command, it means every configuration made so far is good.

**Reload the Prometheus configuration without restarting:**

```bash
curl -X POST http://localhost:9090/-/reload
```

Access Prometheus in browser (if already opened, just reload the page):


`http://<your-prometheus-ip>:9090/targets`


Open Port number 9100 for Monitoring VM

You should now see "Jenkins (1/1 up)" "node exporter (1/1 up)" and "prometheus (1/1 up)" in the prometheus browser.


**step 5: Install Grafana**

You are currently in /etc/Prometheus path.


```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https software-properties-common
cd
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
#You should see OK when executed the above command.
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
sudo apt-get update
sudo apt-get -y install grafana
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```

Press control+c to come out


Access grafana in browser using your server's IP and port 3000:

`http://<your-server-ip>:3000`

Default username and password is "admin"

You can Set new password or you can click on "skip now".

Click on "skip now" (If you want you can create the password)


**Step 6: Add Prometheus Data Source:**

To visualize metrics, you need to add a data source. Follow these steps:

- Click on the gear icon (⚙️) in the left sidebar to open the "Configuration" menu.

- Select "Data Sources."

- Click on the "Add data source" button.

- Choose "Prometheus" as the data source type.

- In the "HTTP" section:
  - Set the "URL" to `http://localhost:9090` (assuming Prometheus is running on the same server).
  - Click the "Save & Test" button to ensure the data source is working.

**Step 7: Import a Dashboard:**
 
To make it easier to view metrics, you can import a pre-configured dashboard. Follow these steps:

- Click on the "+" (plus) icon in the left sidebar to open the "Create" menu.

- Select "Dashboard."

- Click on the "Import" dashboard option.

- Enter the dashboard code you want to import (e.g., code 1860).

- Click the "Load" button.

- Select the data source you added (Prometheus) from the dropdown.

- Click on the "Import" button.

You should now have a Grafana dashboard set up to visualize metrics from Prometheus.

Grafana is a powerful tool for creating visualizations and dashboards, and you can further customize it to suit your specific monitoring needs.

That's it! You've successfully installed and set up Grafana to work with Prometheus for monitoring and visualization.