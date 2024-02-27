# CI_CD_Deployment_WITH_GitHUB_ACTION_WORKFLOW_AND_EC2_AND_docker

![Image Alt text](/images/blog.jpg)

## Introduction:-

In today's fast-paced development environment, efficient and reliable software delivery is crucial. Continuous Integration (CI) and Continuous Deployment (CD) pipelines are essential tools for achieving this goal. In this blog post, we'll explore the implementation of a CI/CD pipeline using GitHub Actions and learn how to deploy the application onto an EC2 instance.

## Understanding CI/CD:-

## Continuous Integration (CI):-
CI involves automatically integrating code changes from multiple contributors into a shared repository. This process helps identify and address integration issues early in the development cycle. GitHub Actions provides a powerful platform for automating CI workflows.

## Continuous Deployment (CD):-
CD extends the CI process by automatically deploying code changes to production or staging environments after passing the CI tests. This minimizes manual intervention, reduces deployment errors, and accelerates the delivery pipeline


# Prerequisites:-
## 1. Amazon Web Services (AWS) Account:-
Create an AWS account if you don't have one: AWS Sign Up.
Familiarize yourself with AWS services, especially EC2.

## 2. Launch EC2 instance and  create Key Pair:-
Generate an EC2 instance and key pair through the AWS Management Console.
Navigate to EC2 and launch a new instance. Choose an Amazon Machine Image (AMI) based on your project requirements.

During the launch, configure the security group to allow incoming SSH (port 22) connections,http request (port 80). This ensures that you can connect to your EC2 instance securely and see the your website on browser.

Download the private key (.pem file) securely; you will use this key to connect to your EC2 instance.

## 3.Connect to EC2 Instance:
Use the EC2 key pair you generated to connect to your instance:
Copy code

```ssh -i /path/to/your/key.pem ec2-user@your-ec2-public-ip```

## 4. GitHub Account:-
Create a GitHub account if you don't have one.Set up a private repository for your project.

### GitHub Setup:
### Generate SSH Key:
While logged into your EC2 instance, generate an SSH key pair:
Copy code

```ssh-keygen -t rsa -b 4096 -C "your_email@example.com"```

Add the generated public key (~/.ssh/id_rsa.pub) to your GitHub account to allow the EC2 instance to access your private repository.
go to github account and go on >your profile>settings>ssh key and Gpg key>add key>paste your ec2 id_rsa.pub key and create.

## 5. Clone Private Repository:
Clone your private GitHub repository onto your EC2 instance:
Copy code

```git clone git@github.com:your-username/your-repo.git```

now go on github account and go GitHub Actions Workflow:
### Create a Workflow File:
Inside your repository, create a ```.github/workflows/main.yml``` file.
### Define Workflow:
Define a GitHub Actions workflow to build and push your Docker image. This example uses the Docker Build and Push action:
### main.yaml
Copy code 

```
name: CI

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: self-hosted  ##if you want to deploy code on your own ec2 instance so change this name and give self-hosted name.

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Change to directory
        run: cd /home/ubuntu/first/second    #give the path of your code that repo have Dockerfile
        
      - name: first delete Docker image
        run: docker rmi -f myimage:1    ##this command is use for delete image that already created image

      - name: now build your Docker image
        run: docker build -t myimage:1 .

      - name: Remove existing Docker container ##this command is use for delete container that already running container 
        run: docker rm -f mohit

      - name: Run Docker container
        run: docker run -itd --name mohit -p 80:80 myimage:1
```

### Install Docker on EC2:
Connect to your EC2 instance and install Docker.
bash
Copy code

```
sudo apt update -y
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ubuntu ##if you have any user name so replace this username 
```
### Restart the EC2 Instance:
Restart your EC2 instance to apply the Docker group changes.
```
sudo reboot
```
### Run Docker Container:
After your EC2 instance is back online, connect to it and create docker image and run your Docker container.

first you will be in your code and see the Dockerfile you have. then create docker image

```
docker build -t myimage:1 . 
docker run -itd mohit -p 80:80 myimage:1
```
now, your application should be running on your EC2 instance.
if you want to check so copy your public ip and paste the browser and check.

now go your github account and go your repo and click setting option and find action  then 
choose runners and click new self hosted and select your desired os 
by the i like ubuntu so i choose ubuntu system.
after then go to your ec2 terminal and 
copy all command that runners give we and paste ec2 terminal for configure github action connect with ec2 instance.
for example this command :

```
# Create a folder
mkdir actions-runner && cd actions-runner
# Download the latest runner package
curl -o actions-runner-linux-x64-2.313.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.313.0/actions-runner-linux-x64-2.313.0.tar.gz
# Optional: Validate the hash
echo "56910d6628b41f99d9a1c5fe9df54981ad5d8c9e42fc14899dcc177e222e71c4  actions-runner-linux-x64-2.313.0.tar.gz" | shasum -a 256 -c# Extract the installer
tar xzf ./actions-runner-linux-x64-2.313.0.tar.gz

```

```
# Create the runner and start the configuration experience
./config.sh --url https://github.com/mohitgaur1272/ci_cd_deployment_git_ec2 --token BBBLUL6FWWFXPSWRXXR5R6DF3GDW4
# Last step, run it!
./svc.sh
./run.sh
```
```
# Use this YAML in your workflow file for each job
runs-on: self-hosted
```

after this configuration go your repo github action you will see new pipeline and 
As soon as you push the code changes, your pipeline will automatically be triggered and run.

if you have any error in pipelime so check logs and fix bugs and run again your pipeline.








