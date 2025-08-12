🏗 Highly Available Web Application on AWS 

This project demonstrates how to build a *highly available web application* using only the *AWS Management Console*. It includes creating a custom VPC, EC2 instances, a load balancer, and an auto scaling group — all deployed manually via the AWS Console.

---

📦 Architecture Overview

- *VPC* with public subnets across two Availability Zones
- *EC2 Launch Template* to deploy web servers
-  *EC2*
- *Application Load Balancer (ALB)* for traffic distribution
- *Auto Scaling Group (ASG)* for high availability and scalability

---

🌐 AWS Console Setup Guide

1. Create a VPC

- Go to *VPC Dashboard* > *Create VPC*
- Name: WebVPC
- CIDR block: 10.0.0.0/16

2. Create Two Public Subnets

- Subnet 1: 10.0.1.0/24 in us-east-1a
- Subnet 2: 10.0.2.0/24 in us-east-1b
- Enable auto-assign public IP

3. Create and Attach an Internet Gateway

- Go to *Internet Gateways* > Create
- Attach it to WebVPC

4. Create a Route Table

- Create a new route table in WebVPC
- Add route to 0.0.0.0/0 via Internet Gateway
- Associate it with both subnets

5. Create a Security Group

- Go to *EC2 > Security Groups*
- Inbound rules:
  - Allow HTTP (port 80) from 0.0.0.0/0
  - Allow SSH (port 22) from your IP


🚀 EC2 and Load Balancing

6. Create a Launch Template

- Go to *EC2 > Launch Templates*
- AMI: Amazon Linux 2
- Instance type: t2.micro
- Security group: the one created earlier
- Key pair: your existing key (or create one)
- User data:
  bash
  #!/bin/bash
  yum update -y
  yum install -y httpd
  systemctl enable httpd
  systemctl start httpd
  echo "Hello from $(hostname)" > /var/www/html/index.html




7. Create Target Group

- Go to **EC2 Dashboard** > **Target Groups** > **Create Target Group**
- Select **"Instances"** as the target type
- Protocol: **HTTP**, Port: **80**
- VPC: Select your custom VPC
- Do not register any targets yet (the Auto Scaling Group will handle that)

---

8. Create Application Load Balancer (ALB)

- Go to **EC2 > Load Balancers** > **Create Load Balancer** > **Application Load Balancer**
- Name: `WebALB`
- Scheme: **Internet-facing**
- IP type: **IPv4**
- Select **two public subnets** (in different Availability Zones)
- Attach the **security group** created earlier
- Add a **listener** on port 80 that forwards to the target group
- After creation, **note the ALB DNS name**

---

9. Create Auto Scaling Group (ASG)

- Go to **EC2 > Auto Scaling Groups** > **Create Auto Scaling Group**
- Name: `WebASG`
- Use the previously created **Launch Template**
- Select your **VPC** and **both subnets**
- Attach the **Target Group** you created
- Configure group size:
  - Desired: `1`
  - Minimum: `2`
  - Maximum: `4`
- Create the Auto Scaling Group

---

✅ Final Step: Access the Application

- Go to **EC2 > Load Balancers**
- Copy the **DNS name** of your ALB (e.g., `WebALB-1234567890.us-east-1.elb.amazonaws.com`)
- Paste it into your browser


📘 Learning: SPOF vs Virtualization vs Docker

🛑 SPOF (Single Point of Failure)

- A *SPOF* is a single component that, if it fails, causes the entire system or service to become unavailable.
- *Example*: A single EC2 instance running a website. If it goes down, the website becomes unreachable.

🧱 Virtualization

- *Virtualization* enables running multiple virtual machines (VMs) on one physical server using a *hypervisor*.
- *What I explored*:
  - Launched EC2 instances with different AMIs
  - Noted how each EC2 acts like a full operating system, isolated from others
  - Understood that EC2 uses virtualization (Xen or Nitro) for instance management

---
🐳 Docker (Containerization)

- *Docker* is a platform for building, packaging, and running applications in *lightweight containers* that share the host OS kernel.
- *What I did*:
  - Installed Docker on an EC2 instance:
    bash
    sudo yum update -y
    sudo amazon-linux-extras install docker
    sudo service docker start
    sudo docker run hello-world
    
  - Ran containers to test app packaging and isolation

---

🧪 Personal Learning Reflection

While building the highly available web app on AWS, I took time to experiment and learn about core infrastructure technologies:

- *EC2 and Virtualization*: I understood how virtual machines provide full OS environments with complete isolation. Launching and managing EC2 gave me hands-on insight into how virtualization abstracts hardware and scales compute.
- *Docker on EC2*: By installing Docker manually on EC2, I saw firsthand how containers are more lightweight and faster to deploy. I appreciated how containerization simplifies application packaging and replication.
- *SPOF Awareness*: This project highlighted the importance of designing for resilience. I learned how load balancers, availability zones, and auto scaling help eliminate SPOFs in production systems.

This experience bridged the gap between theory and practice, improving my understanding of both traditional VM-based architectures and modern container-based deployments.

---
