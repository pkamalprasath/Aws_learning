![Alt_text](ALB/ApplicationLoadBalancer.drawio.png)

---
# AWS Project: Application Load Balancer (ALB) with Target Group (EC2 Instances)
## Project Overview
In this project, we set up an AWS Application Load Balancer (ALB) with a target group consisting of two EC2 instances. The architecture is secured by using Security Groups that ensure traffic flows only through the ALB to the EC2 instances, preventing direct access to the EC2 instances by their public IP addresses.

## Key Components:
### Application Load Balancer (ALB): Distributes traffic across EC2 instances.
Target Group: Contains 2 EC2 instances to handle web traffic.
Security Groups: Control traffic flow to ensure security.

## Step-by-Step Guide
Step 1: Launch EC2 Instances with User Data  
Launch 2 EC2 instances:  
Open the AWS Management Console.  
Go to EC2 Dashboard > Instances > Launch Instances.  
Choose an AMI, such as Amazon Linux 2  
Instance Type: Choose t2.micro for low-cost testing (or a type suitable for your workload).  
Network: Ensure both instances are in the same VPC.  


## Configure the User Data Script:  
Under the Advanced Details section in the launch configuration page, you will see an option for User Data.  
Paste the following User Data script to automatically install a web server and create an index page.  
For (Amazon Linux 2):  

#!/bin/bash  
#Use this for your user data (script from top to bottom)  
#install httpd (Linux 2 version)  
yum update -y  
yum install -y httpd  
systemctl start httpd  
systemctl enable httpd  
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html  
This script will automatically install the Apache web server and create a basic HTML page on each instance.  
Security Group for EC2:  

## Create a new Security Group (named EC2-SG).
Inbound Rules: Only allow HTTP (port 80) traffic from the ALB Security Group (defined in Step 4).  
Outbound Rules: Allow all outbound traffic (default).  
Step 2: Create an Application Load Balancer (ALB)  

## Go to Load Balancers:
In the AWS Management Console, navigate to EC2 Dashboard > Load Balancers > Create Load Balancer.  
Choose Application Load Balancer (ALB):  
Name: Provide a name for your ALB (e.g., my-alb).  
Scheme: Choose Internet-facing to allow public access.  
Listeners: Set to HTTP on port 80.  
Availability Zones: Select the VPC and the same availability zones as your EC2 instances.  

## Configure ALB Security Group:
Create a Security Group for the ALB (named ALB-SG).  
Inbound Rule: Allow HTTP traffic (port 80) from anywhere (0.0.0.0/0).  
Outbound Rule: Allow all outbound traffic (default).  
Step 3: Create a Target Group  

## Create a Target Group:
Go to Target Groups > Create Target Group.   
Target Type: Choose Instances.  
Target Group Name: Name your target group (e.g., my-target-group).  
Protocol: Set to HTTP.  
Port: Set to 80.  
Health Check Path: Use the default (/) or customize as per your requirements.  
Register EC2 Instances:  
After creating the target group, add both EC2 instances as targets.  
Make sure the instances are healthy based on the health checks.   
Step 4: Configure Security Groups for EC2 Instances   
Update the EC2 Security Group (EC2-SG):  
Go to EC2 Dashboard > Security Groups.  
Modify the Inbound Rule of the EC2-SG:  
Allow HTTP (port 80) traffic only from the ALB Security Group (ALB-SG). This restricts access, ensuring users can't access EC2 instances directly via their public IP addresses.  
Step 5: Test the Setup  

## Get the DNS Name of the ALB:  
After configuring the ALB, it will have a DNS name (e.g., my-alb-123456789.us-west-2.elb.amazonaws.com).  
Navigate to this DNS name in your browser.  

## Verify Access:  
You should see the web pages hosted on your EC2 instances through the ALB.  
If you try to access the EC2 instances directly by their public IP, it should not work, as they are protected by the security group configuration.  

## Summary of Security Groups:
ALB-SG: Allows inbound HTTP traffic from anywhere (0.0.0.0/0).
EC2-SG: Only allows HTTP traffic from the ALB (via ALB-SG). No direct public access is permitted.

## Conclusion
This project demonstrates a secure setup of an Application Load Balancer with two EC2 instances as targets. The EC2 instances are protected by security groups that ensure users can only access them through the ALB, and not via their direct IP addresses.
