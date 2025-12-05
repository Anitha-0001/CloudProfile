# 🚀 AWS DevStack Deploy (CloudProfile Application)

Demonstrates cloud migration of a web application from Local Machine to AWS environment.

## 📌 Project Overview

This project showcases the deployment of a production-ready multi-tier web application (**CloudProfile**) from a local environment (Vagrant-based) to **AWS Cloud** using a **lift and shift strategy**. 



It uses services like EC2, Load Balancer, Auto Scaling, RDS, VPC, along with automation scripts for setup and configuration. The goal is to make the application cloud-ready, scalable, and manageable without upfront infrastructure costs.


**🛠️ Services Used:**

- **EC2 Instances:** Tomcat, MySQL, RabbitMQ, Memcache  
- **Application Load Balancer (ALB)**  
- **Auto Scaling Group**  
- **Amazon S3** (artifact storage)  
- **Route 53** (DNS management)  
- **Amazon Certificate Manager (ACM)**  
- **Security Groups**  
- **Key Pairs**  

---

## 🔗 Architectural Flow

1. Users access your application via a URL (configured in GoDaddy DNS or other registrar).  
2. The URL points to the **ALB endpoint**.  
3. ALB routes **HTTPS traffic** to Tomcat EC2 instances (managed by an **Auto Scaling Group**).  
4. Tomcat instances interact with backend services:  
   - MySQL Instances  
   - RabbitMQ Instances  
   - Memcache Instances  
5. Backend IPs are managed via **Route 53 Private DNS Zones**.  
6. Application artifacts are stored in **S3** and pulled to EC2 during deployment.  
7. Security is enforced using **Security Groups**:  
   - ALB Security Group (HTTPS traffic only)  
   - Tomcat Security Group (HTTP 8080 from ALB only)  
   - Backend Security Group (MySQL, RabbitMQ, Memcache ports restricted)  

---

## ⚙️ Sequence of Setup Instructions

### Step 1: AWS Account Setup
- Log in to your AWS account.  
- Create **key pairs** to access EC2 instances.  

### Step 2: Security Groups
- Create separate security groups for:  
  - Application Load Balancer  
  - Tomcat EC2 instances  
  - Backend services (MySQL, RabbitMQ, Memcache)  
- Configure appropriate **inbound/outbound rules**.  

### Step 3: Launch EC2 Instances
- Launch EC2 instances for:  
  - Tomcat (application layer)  
  - MySQL, RabbitMQ, Memcache (backend layer)  
- Use **user-data scripts** to automate instance setup (install packages, start services).  

### Step 4: Configure Route 53
- Create **Private DNS Zones** for backend servers:  
  - `db01` → MySQL IP  
  - `mc01` → Memcache IP  
  - `rmq01` → RabbitMQ IP  
- Ensure Tomcat instances resolve backend servers via **private DNS names**.  

### Step 5: Build & Upload Application
- On your local machine, build the artifact:  
  mvn install
- Configure AWS CLI (if not already configured)
  aws configure
- Upload the artifact to S3:
  aws s3 cp target/<artifact>.war s3://<your-bucket-name>/

### Step 6: Deploy Application to EC2
- Download the artifact from S3 to Tomcat instances.  
- Deploy the `.war` file on Tomcat.  

### Step 7: Configure Application Load Balancer
- Create an **Application Load Balancer (ALB)** with HTTPS listener.  
- Attach Tomcat instances as targets.  
- Associate the ALB with the **ACM certificate** for SSL.  

### Step 8: Domain Mapping
- Point your GoDaddy (or other registrar like Hostinger) DNS entry to the **ALB endpoint**.  

### Step 9: Auto Scaling
- Create an **Auto Scaling Group** for Tomcat instances.  
- Configure scale-out/scale-in policies based on CPU or traffic load.  

### Step 10: Validation
- Access the website via the **custom URL**.  
- Verify HTTPS, backend connectivity, and scaling behavior.  

---

## ✅ Key Notes
- Each layer (ALB, Tomcat, backend) is isolated via **Security Groups**.  
- **Route 53** ensures backend communication uses private IPs.  
- **S3** is used for artifact storage; EC2 instances pull artifacts during deployment.  
- **Auto Scaling** ensures elasticity.  
- **ACM** enables HTTPS encryption. 

## 📜 License
This project is open-source and free to use, modify, and distribute.
