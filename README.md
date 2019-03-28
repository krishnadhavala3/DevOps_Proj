## krishna_challenge


*Approach Tools

- AWS Cloud
- AWS Cloud Formation
- Puppet Open Source



**Project Scenario_**

For this project, please think about how you would architect a scalable and secure static web application in AWS or another IaaS provider.

Create and deploy a running instance of a web server using a configuration management tool of your choice. The web server should serve one page with the following content.

<html>

<head>

<title>Hello World</title>

</head>

<body>

<h1>Hello World!</h1>

</body>

</html>

Secure this application and host such that only appropriate ports are publicly exposed and any http requests are redirected to https. This should be automated using a configuration management tool of your choice and you should feel free to use a self-signed certificate for the web server.

Develop and apply automated tests to validate the correctness of theserver configuration.

Express everything in code and provide your code via a Git repository in GitHub.


---------------------------------------------------------------------------------------------------------------
### Implementation

**Solution *

1. Create an Instance on AWS (AMI-ami-6d1c2007).
2. Install open source Puppet Master.
3. Define a Apache module - [DevOps_Proj/apache] (https://github.com/munikumare/DevOps_Proj/tree/master/apache) folder contains the apache module scripts.
4. Create Autosign file with the node name of the instance, which automatically signs the nodes.
5. Declare Apache module at /etc/puppetlabs/code/environments/production/manifests/site.pp
```
node default {
  include apache
}
```
6. Create AWS Cloud Formation JSON template to launch an instance over AWS, bootstraped with CloudFormation helper scripts that needs to interprets the metadata - [DevOps_Proj/SSLFNL.json](https://github.com/munikumare/DevOps_Proj/blob/master/SSLFNL.json) file contains the script.
7. Modify the Puppet Node IP of the instance and Puppet Master Server IP accordingly at the config2 section of Metadata.
```
 "config2" : {
            "files" : {
              "/etc/hosts" : {
                     "content" : {"Fn::Join" : ["", [
                     "127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4\n",
                     "::1         localhost localhost.localdomain localhost6 localhost6.localdomain6\n",
                     "192.168.1.18 puppet\n",
                     "192.168.1.105 puppetnode2.training.com mysite.com www.mysite.com\n"

                     ]]}
                     }
            }
          },
```
8. Upload the AWS JSON template to S3 bucket and create the stack, that automatically launches an EC2 Instance.
9. EC2 Instance will be launced with puppet agent, that talks to Puppet Master to run the convergence, download the apache module.
10. Add mysite.com to your local dns, i.e. /etc/hosts file and test the site http://mysite.com



