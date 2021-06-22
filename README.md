# Installation Guide

* _[Azure CLI](#Install-Azure-CLI)_ 
* _[Azure Kubernetes Service](#Azure-Kubernetes-Service)_
* _[Ansible](#Install-Ansible)_
* _[Docker](#Install-Docker)_
* _[Docker Compose](#Install-Docker-Compose)_
* _[Jenkins](#Install-Jenkins)_
* _[Terraform](#Install-Terraform)_


# Install Azure CLI


``curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash``



# Azure Kubernetes Service

Create CLI-machine

````
az group create -n cli-machine-group -l uksouth
az vm create -g cli-machine-group -n cli-machine --image UbuntuLTS --size Standard_B1ms --admin-user kusha
ssh kusha@cli-machine
````

Install Azure CLI 

````
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
````

Login

````
az login
````

Configure

````
az configure
````

Set defaults

````
az configure --defaults location=uksouth
````

Create Resource Group

````
az group create -n k8group 
````

Create AKS cluster

````
az aks create -g k8group -n AKScluster --node-count 1 --node-vm-size Standard_B2s --enable-addons monitoring --generate-ssh-keys
az aks show --name AKSCluster --resource-group k8group
````

Install kubectl

````
sudo az aks install-cli
````

Get credentials

````
az aks get-credentials --resource-group k8group --name AKSCluster
````

Verify

````
kubectl get nodes
````



# Install Ansible

`sudo apt-get update`

`sudo apt install software-properties-common`

``sudo apt-add-repository --yes --update ppa:ansible/ansible``

`sudo apt install ansible`



# Install Docker

```
sudo apt-get update
```

```
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add –
```

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu  $(lsb_release -cs)  stable"
```

```
sudo apt-get update
```

```
sudo apt-get install docker-ce
```

```
sudo usermod -aG docker username
```



# Install Docker Compose

````
sudo apt-get update
````

````
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
````

````
sudo chmod +x /usr/local/bin/docker-compose
````



# Install Jenkins

```
#!/bin/bash
if type apt > /dev/null; then
    pkg_mgr=apt
    java="openjdk-11-jre"
elif type yum /dev/null; then
    pkg_mgr=yum
    java="java"
fi
echo "updating and installing dependencies"
sudo ${pkg_mgr} update
sudo ${pkg_mgr} install -y ${java} wget git > /dev/null
echo "configuring jenkins user"
sudo useradd -m -s /bin/bash jenkins
echo "downloading latest jenkins WAR"
sudo su - jenkins -c "curl -L https://updates.jenkins-ci.org/latest/jenkins.war --output jenkins.war"
echo "setting up jenkins service"
sudo tee /etc/systemd/system/jenkins.service << EOF > /dev/null
[Unit]
Description=Jenkins Server

[Service]
User=jenkins
WorkingDirectory=/home/jenkins
ExecStart=/usr/bin/java -jar /home/jenkins/jenkins.war

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable jenkins
sudo systemctl restart jenkins
sudo su - jenkins << EOF
until [ -f .jenkins/secrets/initialAdminPassword ]; do
    sleep 1
    echo "waiting for initial admin password"
done
until [[ -n "\$(cat  .jenkins/secrets/initialAdminPassword)" ]]; do
    sleep 1
    echo "waiting for initial admin password"
done
echo "initial admin password: \$(cat .jenkins/secrets/initialAdminPassword)"
EOF
```

``sudo visudo``

````
# allow the user jenkins to run any command as sudo without a password
jenkins ALL=(ALL:ALL) NOPASSWD:ALL
````



# Install Terraform

````
sudo apt update && sudo apt upgrade -y
````

````
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
````

````
sudo apt-add-repository "deb [arch=$(dpkg --print-architecture)] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
````

````
sudo apt install terraform
````
