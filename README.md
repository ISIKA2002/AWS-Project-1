### Project - 1: Deploying a Multi-Tier Website Using AWS EC2
To deploy a multi-tier website using AWS EC2 with high availability and auto-scaling, follow these steps:

**Description of Components**
**Route 53:** AWS Route 53 is used for DNS management, directing traffic to the Application Load Balancer (ALB).

**Application Load Balancer (ALB):** The ALB distributes incoming traffic across multiple EC2 instances in different Availability Zones to ensure high availability and fault tolerance.

**Auto Scaling Group:** This ensures that the number of EC2 instances scales up or down based on demand. It maintains a minimum of 2 instances to ensure high availability.

**EC2 Instances:** These instances host the web application (PHP). They are part of the Auto Scaling Group and receive traffic from the ALB.

**RDS (MySQL):** Amazon RDS for MySQL is used to host the database. It ensures high availability, automated backups, and easy scalability.

### Step 1: Launch an EC2 Instance

1. **Login to AWS Console**:
   - Go to the [AWS Management Console](https://aws.amazon.com/console/).

2. **Launch EC2 Instance**:
   - Navigate to the **EC2 Dashboard**.
   - Click **Launch Instance**.
   - Choose an **Amazon Machine Image (AMI)** (e.g., Amazon Linux 2 AMI).
   - Choose an **Instance Type** (e.g., t2.micro for testing purposes).
   - Configure the **Instance Details**.
     - Ensure the instance is in a public subnet.
   - Add **Storage** (default is fine).
   - Add **Tags** (optional).
   - Configure the **Security Group**:
     - Allow HTTP (port 80), HTTPS (port 443), and SSH (port 22) traffic.
   - Review and **Launch** the instance.
   - Download the **Key Pair** and save it securely.

### Step 2: Enable Auto Scaling

1. **Create a Launch Configuration/Template**:
   - Navigate to the **EC2 Dashboard**.
   - Click on **Launch Configurations** under **Auto Scaling**.
   - Create a **Launch Configuration** using the same settings as your EC2 instance.
   - Select the same AMI and instance type.
   - Configure the security group and attach the key pair.

2. **Create an Auto Scaling Group**:
   - Navigate to the **Auto Scaling Groups**.
   - Create an **Auto Scaling Group** with the created launch configuration.
   - Set the minimum and maximum number of instances (e.g., minimum 2, maximum 4).
   - Configure **Scaling Policies**:
     - Target tracking scaling policy to maintain a specified average CPU utilization.

### Step 3: Create an RDS Instance

1. **Launch RDS Instance**:
   - Navigate to the **RDS Dashboard**.
   - Click on **Create Database**.
   - Select **MySQL**.
   - Choose the **DB instance size** (e.g., db.t2.micro for testing).
   - Configure the **Database Settings**:
     - DB instance identifier: `mydbinstance`
     - Master username: `admin`
     - Master password: `intel123`
   - Configure **Connectivity**:
     - Ensure it is in the same VPC as your EC2 instances.
     - Create or select a security group to allow traffic from your EC2 instances.
   - **Create Database**.

### Step 4: Create Database & Table in RDS

1. **Connect to RDS Instance**:
   - Use the **RDS endpoint** to connect to the database.
   - You can use a MySQL client or connect from the EC2 instance.

2. **Create Database and Table**:
   ```sql
   CREATE DATABASE intel;
   USE intel;
   CREATE TABLE data (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(255) NOT NULL,
       value VARCHAR(255) NOT NULL
   );
   ```

### Step 5: Change Hostname in Website

1. **Update PHP Code**:
   - SSH into your EC2 instance.
   - Navigate to the directory where your website code is stored (e.g., `/var/www/html`).
   - Update the database connection settings in your PHP files to use the RDS endpoint:
     ```php
     $servername = "your-rds-endpoint";
     $username = "admin";
     $password = "intel123";
     $dbname = "intel";
     ```

### Step 6: Allow Traffic from EC2 to RDS

1. **Update Security Group for RDS**:
   - Navigate to the **RDS Dashboard**.
   - Modify the security group attached to your RDS instance to allow inbound traffic on port 3306 from the security group of your EC2 instances.

### Step 7: Allow All Traffic to EC2 Instance

1. **Update EC2 Security Group**:
   - Navigate to the **EC2 Dashboard**.
   - Modify the security group attached to your EC2 instances to allow inbound traffic from all sources (only for testing, restrict this in production).

### Step 8: Test the Setup

1. **Verify EC2 Instances**:
   - Ensure that the EC2 instances are running and accessible via HTTP/HTTPS.

2. **Verify RDS Connection**:
   - Ensure that your website can connect to the RDS database and perform read/write operations.

3. **Verify Auto Scaling**:
   - Simulate load to verify that auto-scaling is working as expected.

### Summary

This guide provides the steps to set up a multi-tier architecture on AWS with high availability using EC2, Auto Scaling, and RDS. Follow these steps carefully to ensure a successful deployment:

1. Launch EC2 instance.
2. Set up Auto Scaling.
3. Create an RDS instance.
4. Create database and table in RDS.
5. Update website hostname.
6. Configure security groups.
7. Test the setup.
   
**Architecture Diagram**


                             +----------------------+
                             |      Route 53        |
                             +----------------------+
                                      |
                                      v
                             +----------------------+
                             |      ALB (ELB)       |
                             +----------------------+
                                      |
                                      v
                       +--------------------------+
                       |       Auto Scaling       |
                       |  Group (EC2 Instances)   |
                       +--------------------------+
                       /             |            \
                     /               |              \
                   /                 |                \
      +-------------------+  +-------------------+  +-------------------+
      |     EC2 Instance  |  |     EC2 Instance  |  |     EC2 Instance  |
      |   (Web Server 1)  |  |   (Web Server 2)  |  |   (Web Server 3)  |
      +-------------------+  +-------------------+  +-------------------+
                  |                    |                     |
                  |                    |                     |
                  +-----------------------------------------+
                                      |
                                      v
                             +----------------------+
                             |       RDS (MySQL)    |
                             +----------------------+
                                      |
                                      v
                            +----------------------+
                            |   Database: intel    |
                            +----------------------+
                                      |
                                      v
                            +----------------------+
                            |     Table: data      |
                            +----------------------+






