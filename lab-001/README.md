# Lab-001: Secure and Scalable Web Hosting on AWS  

## Difficulty Level: 2  
**Creation Date:** March 2025  
**Original Author(s):** Pratik Sontakke  
**Contributor(s):**  

## 🎯 Goal  
Deploy a **highly available and secure** web application on AWS using **EC2, ALB, Auto Scaling, RDS (MySQL), and Route 53**.  

## 🏗 Architecture Diagram  
(Use AWS official architecture icons available [here](https://aws.amazon.com/architecture/icons/) to create a visual diagram.)  

## 📖 Overview  

This lab focuses on building a **secure and scalable** web hosting environment using:  

- **VPC** with **2 public & 2 private subnets**  
- **Internet Gateway (IGW)** for external access  
- **NAT Gateway** for private instances to access the internet  
- **Application Load Balancer (ALB)** for traffic distribution  
- **Auto Scaling Group (ASG)** for high availability  
- **RDS (MySQL) in Multi-AZ** for database reliability  
- **Security Groups & IAM roles** for secure access  

---

## 🔹 Steps to Complete the Lab  

### **1️⃣ Create a VPC with Subnets**  
- Create a **VPC** (CIDR: `10.0.0.0/16`)  
- Add **2 public subnets** (`10.0.1.0/24`, `10.0.2.0/24`)  
- Add **2 private subnets** (`10.0.3.0/24`, `10.0.4.0/24`)  

### **2️⃣ Configure Networking**  
- Attach an **Internet Gateway (IGW)** to the VPC.  
- Create a **Public Route Table**, add a route to IGW, and associate public subnets.  
- Create a **NAT Gateway** in a public subnet.  
- Create a **Private Route Table**, add a route to the NAT Gateway, and associate private subnets.  

### **3️⃣ Deploy Application Load Balancer (ALB)**  
- Create an **ALB in public subnets**.  
- Add a **listener on port 80** and forward traffic to EC2 instances.  

### **4️⃣ Set Up Auto Scaling Group (ASG)**  
- Launch EC2 instances in **private subnets**.  
- Configure Auto Scaling policies to maintain **high availability**.  

### **5️⃣ Deploy RDS MySQL in Multi-AZ**  
- Launch an **RDS MySQL instance** in private subnets.  
- Enable **Multi-AZ deployment** for failover protection.  

### **6️⃣ Connect EC2 to RDS & Create a Database**  
- Attach an EC2 instance to **Session Manager**.  
- Install MySQL client on EC2:  
  ```sh
  sudo yum install -y mariadb
  ```
- Connect to RDS instance
  ```sh
  mysql -h <RDS_Endpoint> -u admin -p
  ```
- Create a test database and table
  ```sh
  CREATE DATABASE test_db;
  USE test_db;

  CREATE TABLE Persons (
      PersonID int,
      LastName varchar(255),
      FirstName varchar(255),
      Address varchar(255),
      City varchar(255)
  );
  ```
### **7️⃣ Insert Sample Data into the Table**  
  ```sh
  INSERT INTO Persons (PersonID, LastName, FirstName, Address, City) 
  VALUES (1, 'Sharma', 'Raj', '123 MG Road', 'Mumbai'),
        (2, 'Verma', 'Priya', '456 Park Street', 'Delhi'),
        (3, 'Reddy', 'Anil', '789 Lake View', 'Hyderabad'),
        (4, 'Das', 'Suman', '321 Hill Road', 'Kolkata'),
        (5, 'Patel', 'Nisha', '654 River Lane', 'Ahmedabad');
```

## ✅ Test and Validation  

### Verify ALB is working  
- Open **ALB DNS name** in your browser (`http://<ALB_DNS>`).  
- It should **route requests to EC2 instances**.  

### Check Auto Scaling  
- Increase **CPU load** and verify new instances are launched automatically.  

### Test RDS Connection  
- Reboot the primary RDS instance and verify **failover works**.  

---

## 🧹 Cleanup  
To **avoid unnecessary costs**, delete all resources:  

```sh
aws ec2 terminate-instances --instance-ids <Instance-ID>
aws rds delete-db-instance --db-instance-identifier <DB-Name> --skip-final-snapshot
aws autoscaling delete-auto-scaling-group --auto-scaling-group-name <ASG-Name> --force-delete
aws elb delete-load-balancer --load-balancer-name <ALB-Name>
aws ec2 delete-vpc --vpc-id <VPC-ID>
```
