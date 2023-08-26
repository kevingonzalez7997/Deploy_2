<!DOCTYPE html>
<html>
<head>
  <H1>ReadMe</H1>
</head>
<body>

<h2>Purpose</h2>

<p>In this project, the end goal is to deploy our application manually using Elastic Beanstalk. Additionally, the program is tested through Jenkins, which runs on an EC2 instance to ensure the code is functioning correctly.</p>

<h2>Issues</h2>

<p>Before you start, there are a set of prerequisites to minimize the chances of encountering bugs, especially during testing in Jenkins:</p>

<ul>
  <li>Always run the following commands to ensure everything is updated before installation:</li>
</ul>

<pre>
sudo apt update
sudo apt upgrade
</pre>

<p>If the last two steps fail while testing in Jenkins, it's possible that plugins might not be installed. In addition to the suggested plugins, make sure to install "Pipeline Utility Steps" as well.</p>

<p>In Jenkins, navigate to:</p>

<pre>
Dashboard/Manage_jenkins/Plugins/
</pre>

<p>Here, you can check if "Pipeline Utility Steps" is installed in the "Installed" section. If not, you can easily install it by searching for the plugin in the "Available" plugins section.</p>
![Screenshot 2023-08-26 095323](https://github.com/kevingonzalez7997/Deploy_2/assets/59447523/fbe29937-8e02-457f-b093-1569eabce07d)
<i>This is what it might look like without the plugins</i>
<h2>Steps</h2>

<h3>Jenkins</h3>

<p>First, run the code in Jenkins to ensure everything is functioning correctly before proceeding with manual deployment using ElasticBeanstalk.</p>

<ul>
  <li>Launch an EC2 instance on AWS</li>
  <li>Update and/or upgrade packages</li>
  <li>Install "openjdk-8-jdk"</li>
  <li>Install "python3.10-venv" (this is important to install before proceeding)</li>
  <li>Add Jenkins repository and install Jenkins</li>
  <li>Install "Pipeline Utility Steps" from the plugin section</li>
</ul>

<h3>Getting the Zip File</h3>

<p>Once the program has passed all stages, follow the last step in the “Packaging the Output Files” section.</p>

<p>From your local PC, navigate to the terminal and change the directory to "ssh".</p>

<p>If no authorization key has been generated yet, generate one now and copy the PUBLIC key (hint: it ends in .pub).</p>

<p>In your EC2 instance, make sure to add the key to the authorization file in the ssh directory.</p>

<p>Now you can request the file. Go back to the local terminal and run the following command:</p>

<pre>
scp user@publicIP:File/location/can/be/found/in/jenkins/console/ /path/on/local/terminal/
</pre>

<h3>IAM</h3>

<p>Two roles are needed in order to run: one for Beanstalk and one for the EC2 instance.</p>

<p>The necessary permissions are AWSElasticBeanstalkWebTier, workerTier, and MulticontainerDocker.</p>

<h3>Setting up ElasticBeanstalk</h3>

<p>Now let's set up ElasticBeanstalk to manage traffic automatically:</p>

<ul>
  <li>Create an application:</li>
  <ul>
    <li>Give it a name</li>
    <li>Pick the platform (Python 3.9)</li>
    <li>Upload the zipped file that was downloaded from Jenkins (upload local file)</li>
  </ul>
  <li>Select the ElasticEC2 profile created in the IAM section</li>
  <li>Pick the default VPC</li>
  <li>Choose the availability zone (us-east-1a)</li>
  <li>Pick storage type (general purpose SSD)</li>
  <li>Set minimum storage to 10GB</li>
  <li>Choose instance type: “T.2 MICRO”</li>
  <li>Submit and check health status</li>
</ul>

<p>If everything worked correctly, you should now have access to the domain.</p>

</body>
</html>


