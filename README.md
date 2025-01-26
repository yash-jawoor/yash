<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Development Setup</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            color: #333;
            line-height: 1.6;
            margin: 0;
            padding: 20px;
        }
        h1, h2 {
            color: #2c3e50;
        }
        code {
            background-color: #ecf0f1;
            padding: 5px;
            border-radius: 5px;
            font-family: "Courier New", Courier, monospace;
        }
        pre {
            background-color: #34495e;
            color: #ecf0f1;
            padding: 20px;
            border-radius: 5px;
            font-family: "Courier New", Courier, monospace;
            overflow-x: auto;
        }
    </style>
</head>
<body>
    <h1>Development Environment Setup</h1>

    <section>
        <h2>Developer - Git Config</h2>
        <pre><code>mkdir jonny
cd jonny
git init
ls -a
ssh-keygen
copy keys->deploy keys
git remote add origin --
git pull origin master
nano index.html
git status
git commit -m " "
email/username
git push origin master</code></pre>
    </section>

    <section>
        <h2>Jenkins Setup</h2>
        <pre><code>#!/bin/bash
sudo apt update
sudo apt install -y fontconfig openjdk-17-jre
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
sudo apt-get install -y git</code></pre>

        <pre><code>publicip:8080
create freestyle project 
add git links
add webhook - link/github-webhook/
commit and push from dev
/var/www$ sudo setfacl -m jenkins:rwx html/
getfacel html/
Cp * /var/www/html -> jenkins shell
access pubip of webserver</code></pre>
    </section>

    <section>
        <h2>Docker Setup</h2>
        <pre><code>mkdir docker
cd docker
nano Dockerfile
FROM ubuntu/apache2
COPY . /var/www/html
sudo docker build -t image .
dupsession
sudo docker images
sudo docker run -d --name cont -p 81:80 jonny/image
sudo docker login
sudo docker push jonny/image</code></pre>

        <pre><code>Webserver
install docker.io
sudo usermod admin -aG docker
docker pull jonny/image
sudo docker run -d --name cont -p 82:80 jonny/image
add jenkins pub key to server auth key
execute shell 
docker build -t jonny/image .
docker login -u jonny -p "password"
docker push jonny/push
ssh admin@172.31.15.116 << 'EOF'
  docker stop abc || true  
  docker rm abc || true    
  docker pull jonny/image
  docker run -d --name cont -p 83:80 jonny/image
EOF</code></pre>
    </section>

    <section>
        <h2>Ansible Setup</h2>
        <h3>Machine 1: Ansible Mac (Debian)</h3>
        <pre><code>sudo apt install ansible
mkdir ansible
cd ansible
ssh-keygen->target machine auth</code></pre>

        <h3>Machine 2: Target Mac (Debian)</h3>
        <pre><code>sudo apt update
ssh-keygen
sudo apt install python3</code></pre>

        <h3>Machine 1: Ansible Mac</h3>
        <pre><code>cd ansible
nano inventory.ini
[web]
172.31.6.114 admin_user=admin
(Target mac private ip) 
ansible -i inventory.ini web -m ping</code></pre>
        <p><strong>Output:</strong> Success</p>

        <pre><code>nano deploy-apache2.yaml
---
- name: Ansible Demo
  hosts: web
  become: true
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes

    - name: Install Apache2
      apt:
        name: apache2
        state: present

    - name: Ensure Apache is started and enabled
      service:
        name: apache2
        state: started
        enabled: true</code></pre>

        <pre><code>ansible-playbook -i inventory.ini deploy-apache2.yaml
Output :Ok</code></pre>

        <pre><code>ssh admin@172.31.6.114 (target  mac private ip )
Login Succesfull</code></pre>

        <h3>Machine 2: Target Mac</h3>
        <pre><code>sudo which apache2</code></pre>

        <h3>Go to Browser:</h3>
        <pre><code>Enter Target mac public ip : 80
See default page</code></pre>
    </section>

    <section>
        <h2>Terraform Setup</h2>
        <pre><code>Terraform Installed on Windows
AWS Account
AWS CLI Configured
Extract the .zip file to a folder (e.g., C:\terraform).
Add the folder to your system's PATH environment variable:
Right-click on This PC > Properties > Advanced system settings > Environment Variables.
Under System variables, select Path and click Edit.
Add the path to your Terraform folder (e.g., C:\terraform)

aws configure
Enter your AWS Access Key ID, Secret Access Key, default region, and output format when prompted.

Create a new folder for your Terraform configuration, e.g., C:\terraform-ec2.
Open the Command Prompt or PowerShell in this directory.
Create the Terraform Configuration File:

Inside the terraform-ec2 directory, create a file named main.tf.
Add the following configuration to main.tf:

provider "aws" {
  region = "us-west-2"  # Replace with your desired AWS region
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"  # Replace with a valid AMI for your region
  instance_type = "t2.micro"  # Instance type, you can change as needed

  tags = {
    Name = "TerraformExampleInstance"
  }
}

output "instance_ip" {
  value = aws_instance.example.public_ip
}

terraform init
terraform plan
terraform apply</code></pre>
    </section>

</body>
</html>
