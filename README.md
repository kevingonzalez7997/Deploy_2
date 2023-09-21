#  Deployment 2
August 15. 2023

Kevin Gonzalez

## Purpose:

In this project, the objective is to deploy an application manually using Elastic Beanstalk. The process has been enhanced by cloning the code directly from a GitHub repository during testing in Jenkins. Jenkins is used to extract the code, build it, and then zip the source code. This approach minimizes the margin for error by zipping and uploading the code after it has undergone testing.

## Prerequisites:

- Have EC2 instances running with Ubuntu
- Best practice have the system up to date before installing anything
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
- After accessing Jenkins through port 8080, install the suggested plug-ins
- In addition to the suggested plugins, Pipeline Utility Steps plugin is also required. It can be installed by:
  -  `Dashboard > Managed Jenkins > Plugins > Available plugins`

### 2. Connecting GitHub to Jenkins 

- Most people use GitHub as their repository platform. Compared to the first deployment the program will not be uploaded locally to Jenkins. The code will be pulled from a GitHub repository that we have created as it is a more practical approach.
- create a new item, and select pipeline
  - Pipeline script from SCM
      - Then Git (As the code will be pulled from the repo)
- Copy and import the Repository URL where the application source code is located
- User Name will be Github user and the password is the generated key in GitHub
- Branch to build should be */main

### 3. ssh the Zip File from EC2 to local

- Once the program has passed all stages, follow the last step in the “Packaging the Output Files” section to complete the zip process 
- From your local PC, navigate to the terminal and cd to "ssh"
     - `cd .ssh`
- If no authorization key has been generated yet, generate one now and copy the PUBLIC key (hint: it ends in .pub)
     - `ssh-keygen`
- In the EC2 instance, make sure to add the PUBLIC key to the `authorization_file` located in the ssh directory
- Go back to the local terminal and run the following command:
  - `scp user@publicIP:File/location/can/be/found/in/jenkins/console/ /path/on/local/terminal/`

### 4. IAM Roles
- IAM roles in AWS serve a crucial purpose in managing and securing access to AWS resources
- Two roles are needed: one for Elastic Beanstalk and one for the EC2 instance
- Starting with Elastic BeanStalk
  - Select type Elastic
  - Select Elastic BeanStalk
- For the EC2 start by selecting EC2
     - The necessary policies are AWSElasticBeanstalkWebTier, AWSElasticBeanstalkWorkerTier, and AWSElasticBeanstalkMulticontainerDocker

### 5. Deploy AWS Elastic BeanStalk

- Navigate to AWS Console then Elastic Beanstalk service
- Create Application
- Configuration details:
     - Platform: Python
     - Python 3.9 running on 64bit Amazon Linux 2023
     - Upload your zipped file that was ssh from the instance
     - Set ec2 Instance Profile to Elastic-EC2 (This is the role created earlier)
     - VPC: Default VPC
     - Availability Zone: us-east-1a
     - Root volume: General Purpose (SSD) > 10GB
## Observations

- In Jenkins when the code was built there were four different stages that it went through
     1. Checkout SCM: Jenkins connecting to GitHub and cloning the most recent repository
     2. Build: Jenkins downloaded all the required files to run the program
     3. Test: Jenkins ran a test file "test_app.py::test_home_page PASSED " and returned passed
     4. Packaging the output files: Jenkins zips file after user confirmation

![Screenshot 2023-09-20 172859](https://github.com/kevingonzalez7997/Deploy_2/assets/59447523/d22db87d-48b6-4500-bae1-5775d26ec73f)
        
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

There is room for improvement in this CI/CD pipeline. Firstly, the code is still being uploaded manually and locally to the Elastic LoadBalancer. To ensure the success of the CI/CD pipeline, any process that can be automated should be automated. Furthermore, we can enhance automation by enabling the pipeline to rebuild, test, and deploy automatically whenever a code change is made. Additionally, it would be good practice to incorporate a monitoring system to minimize downtime.
