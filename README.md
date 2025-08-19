
# AWS Web Application with Auto Scaling and Load Balancer

This project demonstrates a scalable and highly available web application architecture on AWS. It leverages Elastic Load Balancing (ELB), Auto Scaling Groups (ASG), and EC2 instances to ensure reliability, fault tolerance, and performance under varying traffic loads.

## 🚀 Features

- Elastic Load Balancer for distributing traffic
- Auto Scaling Group for dynamic instance management
- EC2 instances with custom launch configurations
- Security groups for controlled access


## 🧱 Architecture Overview

The infrastructure is designed to handle horizontal scaling and ensure high availability across multiple Availability Zones.

🔗 **View the AWS Architecture Diagram**  
[[Lucidchart Diagram](https://lucid.app/lucidchart/d3d4a065-6e75-4896-b234-f00387908262/edit?viewport_loc=-2803%2C-1569%2C6244%2C3066%2C0_0&invitationId=inv_a4a0273e-8b9d-4f10-92b2-0eb31cdf2c0b)](https://lucid.app/lucidchart/d3d4a065-6e75-4896-b234-f00387908262/edit?viewport_loc=-741%2C-928%2C3813%2C1872%2C0_0&invitationId=inv_a4a0273e-8b9d-4f10-92b2-0eb31cdf2c0b)

<img width="1325" height="772" alt="Screenshot 2025-08-19 234043" src="https://github.com/user-attachments/assets/ed4feb1b-c2ac-455e-9c35-8a5bb2795339" />


## 📦 Project Structure

1. EC2 Instance Setup and AMI Creation
- Launched a t2.micro EC2 instance (Free Tier eligible) using Amazon Linux 2 AMI.

 ![1](https://github.com/user-attachments/assets/ae7a5421-db6b-4280-9578-5f26cbf9bfc0)

- Installed Apache Web Server using user data script.
- User data script used:
#!/bin/bash yum update -y
yum install -y httpd systemctl start httpd systemctl enable httpd
echo "Hello from $(hostname -f)" > /var/www/html/index.html

![2](https://github.com/user-attachments/assets/59ad52c4-4025-4443-aa47-d515ed8b8e31)


- Created an AMI from this EC2 instance named 'WebApp-AMI'. 
 
![3](https://github.com/user-attachments/assets/95b384ff-b132-4b46-a1ec-a4d4652f7a02)

2. Launch Template Creation


- Created a Launch Template named 'web-server-template'.
- Used the previously created AMI.
- Specified t2.micro as instance type.
- Attached the security group allowing HTTP (port 80) and SSH (port 22).

 
![4](https://github.com/user-attachments/assets/0fa3f27d-3f09-47c3-9226-a4f086e4fe51)


3. Load Balancer and Target Group


- Created an Application Load Balancer (ALB) named 'Web-ALB' in the default VPC.
- Selected subnets in multiple Availability Zones (1a, 1b, 1c).
- Security group allowed HTTP access from 0.0.0.0/0.
- Created a Target Group with HTTP protocol and health check path '/'.

<img width="860" height="402" alt="5" src="https://github.com/user-attachments/assets/ec199901-b482-41ae-82a5-fb518e358b57" />

4. Auto Scaling Group (ASG)


- Created an Auto Scaling Group using the Launch Template.
- Attached to the ALB and Target Group.
- Min size: 1, Desired capacity: 1, Max size: 3.
- Target tracking policy: Average CPU utilization = 5%.

 ![6](https://github.com/user-attachments/assets/7436d67e-5bb6-4d09-9840-38762495fada)




5. EBS Volume Attachment


- Created a 10GB General Purpose (gp2) EBS volume.


![7](https://github.com/user-attachments/assets/8f671ad6-d498-4f79-bf3b-03cc48825886)
- Attached the volume to the EC2 instance (device name: /dev/sdf → /dev/nvme1n1).
  
![8](https://github.com/user-attachments/assets/c1b2f2ef-3ef7-4634-a9b1-e218700e09b9)

- Formatted the volume using XFS and mounted it on /mydata.

 ![9](https://github.com/user-attachments/assets/bac519a7-8610-4f5c-b8c1-c7b43c3cec7d)



- Added mount entry to /etc/fstab for persistence after reboot.


  ![10](https://github.com/user-attachments/assets/752d3982-9151-40f1-bf1e-cff09e27714d)



- Created an SNS topic named 'auto-scaling-alerts'.
- Subscribed an email (Example@gmail.com) and confirmed it.
- Configured Auto Scaling Group to send notifications for launch/terminate/fail events to the SNS topic.

 ![12](https://github.com/user-attachments/assets/fb712ce6-7c83-4201-9927-ef13a8a85628)

7. Load Testing & Auto Scaling Verification


- Used Apache Benchmark (ab) to simulate traffic:    “ ab -n 1000 -c 100 http://<ALB-DNS>/ “

 
![13](https://github.com/user-attachments/assets/2218344f-4cf5-4f85-aed7-e56843ff7b3b)


- Observed CPU utilization via CloudWatch. 

- Verified that new EC2 instances launched automatically when CPU > 5% (Scale-OUT)

![14](https://github.com/user-attachments/assets/485d4aa7-0da7-4914-aca8-203e0453dbdc)

 ![15](https://github.com/user-attachments/assets/b03cae41-8e93-443f-9499-a3087ba3611d)


 - Verified that new EC2 instances removed automatically when CPU < 5% (Scale-IN)

![16](https://github.com/user-attachments/assets/c4d97a6f-cc18-46f2-9038-a27a1a0a46b8)
![17](https://github.com/user-attachments/assets/fe06f15e-0e6c-47fe-83c9-a264bb95994e)


- Verified that new EC2 instances removed automatically when CPU < 5% (Scale-IN)


 

 
