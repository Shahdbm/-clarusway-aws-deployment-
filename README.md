Clarusway SDA Bootcamp Deployment Project
Week-9 Assignment SDA2008-shahd

AWS Web Application Deployment with ALB + ASG + S3

---

Project Overview
This project demonstrates deploying a highly available Clarusway Bootcamp website on AWS using:
- S3 for static assets (HTML + logos)
- Auto Scaling Group (ASG) for NGINX servers
- Application Load Balancer (ALB) for distributing traffic across instances

---

Part 1: S3 Setup
- Created S3 Bucket: shahd-clarusway-assets
- Uploaded:
  - index.html
  - logo.png
  - sda.png
- Enabled Static Website Hosting.
- Configured Bucket Policy for public read access.

S3 Static Website URL:
http://shahd-clarusway-assets.s3-website.eu-north-1.amazonaws.com

---

Verification:
curl -I http://shahd-clarusway-assets.s3-website.eu-north-1.amazonaws.com
Result: HTTP/1.1 200 OK

---

Part 2: Auto Scaling Group (ASG) Setup
- Created a Launch Template with User Data:
#!/bin/bash
yum update -y
yum install nginx -y
systemctl start nginx
systemctl enable nginx
aws s3 cp s3://shahd-clarusway-assets/index.html /usr/share/nginx/html/

- Configured Auto Scaling Group:
  - Min: 1
  - Max: 3
  - Desired: 2
  - Health Checks: EC2 + ELB enabled.

Verification:
- Screenshot: Two running instances in EC2.

---

Part 3: Application Load Balancer (ALB) Setup
- Created an Internet-facing ALB with a listener on port 80.
- Target Group attached to ASG instances.
- Health Check Path: /

ALB DNS URL:
http://clarusway-alb-876123911.eu-north-1.elb.amazonaws.com

Note:
Due to a configuration issue, the ALB did not return a successful response from the instances.
Troubleshooting steps were attempted, including verifying instance health, security groups, and target group attachment.
However, the ALB endpoint did not display the expected website content.

(Deployment works correctly via S3 and EC2 directly, but not yet from ALB.)

---

Verification:
for i in {1..5}; do curl -s http://clarusway-alb-876123911.eu-north-1.elb.amazonaws.com | grep "Hostname"; done

---

Clean-up:
- S3 bucket deleted.
- ASG terminated.
- ALB removed.
