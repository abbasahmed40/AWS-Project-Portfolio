# AWS 101 Workshop: Setting Up a Private Web Server

This project demonstrates the implementation of core AWS services and architecture through a hands-on workshop provided by AWS. The primary goal of this project is to design, deploy, and administer a fault-tolerant and secure web application architecture on AWS.


## Architecture








The architecture for the project includes:
- **VPC**: A secure, isolated network for AWS resources.
- **Public Subnets**: For NAT Gateways and Load Balancers.
- **Private Subnets**: Hosting web servers.
- **NAT Gateway**: Allowing instances in private subnets to access the internet.
- **Application Load Balancer (ALB)**: For traffic distribution.
- **Amazon S3**: To store web server data and artifacts securely.
- **Scaling with Auto Scaling Group (ASG)**: (Optional challenge).


---




## Steps


### Setup Networking (VPC)
1. Create a new **VPC** with the CIDR block `10.0.0.0/16`.
2. Add public subnets in two availability zones:
   - Subnet A: `10.0.0.0/20`
   - Subnet B: `10.0.16.0/20`
3. Add private subnets:
   - Subnet A: `10.0.128.0/20`
   - Subnet B: `10.0.144.0/20`
4. Attach an **Internet Gateway** to your VPC.
5. Create a **NAT Gateway** in each public subnet.




---

### Resource Security (Security Groups)
1. Define security groups:
   - **ALB Security Group**: Allow HTTP/HTTPS access.




   - **Web Server Security Group**: Allow traffic from the ALB and private IPs.




---

### Access Management (IAM)
1. Create an IAM role for the EC2 instance to:
   - Access S3.
   - Interact with AWS Systems Manager (SSM).


    

---

### Deploy Compute (EC2)
1. Launch EC2 instances in private subnets.
2. Assign the IAM role to the instances.
3. Use Amazon Linux AMI or a similar OS.
4. Enter the below code in user data field

`#!/bin/bash
yum update -y
# Install Session Manager agent
yum install -y https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm
systemctl enable amazon-ssm-agent
# Install and start the php web server
dnf install -y httpd wget php-json php
chkconfig httpd on
systemctl start httpd
systemctl enable httpd

# Install AWS SDK for PHP
wget https://docs.aws.amazon.com/aws-sdk-php/v3/download/aws.zip
unzip aws.zip -d /var/www/html/sdk
rm aws.zip

#Install the web pages for our lab
if [ ! -f /var/www/html/index.html ]; then
rm index.html
fi
cd /var/www/html
wget https://ws-assets-prod-iad-r-iad-ed304a55c2ca1aee.s3.us-east-1.amazonaws.com/2aa53d6e-6814-4705-ba90-04dfa93fc4a3/index.php

# Update existing packages
dnf update -y`





---
### Adminstrator Webserver (SSM) 
1. On the Ec2 Dashbord, select the instance.
2. Select the Connect.
3. Select the Session Manger tab and click connect.



Run the code 
`echo -n 'Private IPv4 Address: ' && ifconfig enX0 | grep -i mask | awk '{print $2}'| cut -f2 -d: && \
echo -n 'Public IPv4 Address: ' && curl checkip.amazonaws.com`



--

### Load Balancing (ALB)
1. Set up an **Application Load Balancer** in public subnets.
2. Configure a target group with your EC2 instances.




---

### Storage (S3)
1. Use S3 to store static assets for the web server.
2. Upload these files to bucket.[Download the required files]( https://ws-assets-prod-iad-r-iad-ed304a55c2ca1aee.s3.us-east-1.amazonaws.com/2aa53d6e-6814-4705-ba90-04dfa93fc4a3/UnzipAndUpload.zip)






---




Finally Test the WebServer copy the DNS name from the Loab Balancer page and Past in to the new browser tab.



## References
- [AWS 101 workshop]( https://catalog.workshops.aws/aws101/en-US/1-getting-started)


