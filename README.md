#  Deployment 2
August 15. 2023

Kevin Gonzalez

## Purpose:

In this project, the goal is to deploy an application manually using Elastic Beanstalk. The process has been improved by sourcing the code directly from GitHub repository when testing in Jenkins. Jenkins is used to source code, build and zip the source code. This will minimize 
## Prerequisites:

- Have EC2 instances running with Ubuntu
- Best pratice have system up to date before installing anything
     -`sudo apt update`
     -`sudo apt updgrade`

## Steps

### 1. Install Jenkins
- Jenkins is a popular open-source continuous integration (CI) server. It is used to build, test, and deploy software projects.
- Install Jenkins Server using instructions [HERE](https://pkg.jenkins.io/debian/)
- Install the required version of Python:
     - `sudo apt install python3.10-venv`
- You will need to get a key to set up Jenkins for the first time, run:
     -  `sudo cat  /var/lib/jenkins/secrets/initialAdminPassword`
- After accesing Jenkins through port 8080, install suggested plug-ins
- In addition to the suggested plugins, Pipeline Utility Steps plugin is also required. It can be installed by:
  -  `Dashboard > Managed Jenkins > Plugins > Available plugins`

### 2. Connecting GitHub to Jenkins 

- Most people use GitHub as their repository platform. Compared to the first deployment the program will not be uploaded locally to jenkins. The code will be pulled from a GitHub repository that we have created as it is a more practical approach.
- create a new item, and select pipeline
  - Pipeline script from SCM
      - Then Git (As the code will be pulled from the repo)
- Copy and import the Repository URL where the application source code is located
- User Name will be Github user and the password is the generated key in GitHub
- Branch to build should be */main

### 3. ssh the Zip File from EC2 to local

- Once the program has passed all stages, follow the last step in the “Packaging the Output Files” section to complete zip process 
- From your local PC, navigate to the terminal and cd to "ssh"
     - `cd .ssh`
- If no authorization key has been generated yet, generate one now and copy the PUBLIC key (hint: it ends in .pub)
     - `ssh-keygen`
- In the EC2 instance, make sure to add the key to the `authorization_file` located in the ssh directory
- Go back to the local terminal and run the following command:
  - `scp user@publicIP:File/location/can/be/found/in/jenkins/console/ /path/on/local/terminal/`

### 4. IAM Roles
- IAM roles in AWS serve a crucial purpose in managing and securing access to AWS resources
- Two roles are needed to run: one for Elastic Beanstalk and one for the EC2 instance
- Starting with Elastic BeanStalk
  - Select type Elastic
  - Select Elastic BeanStalk
- For the EC2 start by selcting EC2
     - The necessary policies are AWSElasticBeanstalkWebTier, AWSElasticBeanstalkWorkerTier, and AWSElasticBeanstalkMulticontainerDocker

### 5. Deploy AWS Elastic BeanStalk

- Navigate to AWS Console then Elastic Beanstalk service
- Create Application
- Configuration details:
     - Platform: Python
     - Python 3.9 running on 64bit Amazon Linux 2023
     - Upload your zipped file that was ssh from the instance
     - Set ec2 Instance Profile to Elastic-EC2 (This is the role reated earlier)
     - VPC: Default VPC
     - Availability Zone: us-east-1a
     - Root volume: General Purpose (SSD) > 10GB

## Troubleshooting 

Before you start, there are a set of prerequisites to minimize the chances of encountering bugs, especially during testing in Jenkins:</p>
Run the following commands to ensure everything is updated before installation:
  - `sudo apt update `
  -  `sudo apt upgrade `
If the last two steps fail while testing in Jenkins, it's possible that plugins might not be installed. In addition to the suggested plugins, make sure to install "Pipeline Utility Steps" as well

Here, you can check if "Pipeline Utility Steps" is installed in the "Installed" section. If not, you can easily install it by searching for the plugin in the "Available" plugins section
This is what it might look like without the plugins
![Screenshot 2023-08-26 095323](https://github.com/kevingonzalez7997/Deploy_2/assets/59447523/fbe29937-8e02-457f-b093-1569eabce07d)




## Optimization 

There is room for improvements in this CICD pipeline. For starters the code is still being manually and locally uploaded into the Elastic Load balancer. In order for CICD pipeline to be successful, any process that could be automated should be. We are still able to automate the deployment further more by making it rebuild, test, and deploy when a change is made to the code. we should also consider adding a monitor system in case something crashes.
