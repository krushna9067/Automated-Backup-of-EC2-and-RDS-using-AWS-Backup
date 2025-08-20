

---

# 🛡️ Automated Backup of EC2 and RDS using AWS Backup

## 📌 Project Overview

This project demonstrates how to **automate backup and recovery of AWS services** using **AWS Backup**.
We’ll create and configure a **centralized backup plan** to protect:

* An **EC2 instance** (with a web server and test file)
* An **RDS MySQL database** (with sample data)

👉 Goal: Ensure **regular, reliable backups** with minimal manual effort — a **key requirement in real-world production environments**.

---

## 📚 Table of Contents

1. Infrastructure Setup

   * Launch EC2
   * Launch RDS
2. Install Web Server & Add Test Data
3. Create and Insert Data in RDS
4. Set Up AWS Backup

   * Backup Vault
   * Backup Plan
   * Assign Resources
5. Trigger Backup and Validate
6. View Recovery Points

---

## ✅ 1. Infrastructure Setup

### 🔸 Launch EC2 Instance

* Go to **EC2 > Launch Instance**
* Name: `backup-ec2`
* AMI: Amazon Linux 2 (or Ubuntu)
* Instance type: `t2.micro` (Free Tier)
* Key pair: Use existing `.pem` file or create a new one
* Security Group: Allow

  * Port 22 (SSH)
  * Port 80 (HTTP)
* Click **Launch Instance**

---

### 🔸 Launch RDS Instance (MySQL)

* Go to **RDS > Databases > Create database**
* Engine: **MySQL**
* Template: **Free Tier**
* DB Instance ID: `backupdb`
* Master username: `admin`
* Master password: `Admin12345!` (use your own secure password)
* DB class: `db.t3.micro`
* Storage: `20GB (General Purpose SSD)`
* Enable **Public access**
* Select same **VPC** as EC2
* Security Group: Allow **Port 3306**
* Click **Create Database**
* Wait until status is **Available**

---

## ✅ 2. Install Web Server & Add Test Data

### 🖥️ Connect to EC2 via SSH

```bash
ssh -i "your-key.pem" ec2-user@<EC2-PUBLIC-IP>
```

### 🛠️ Install Apache (Amazon Linux 2)

```bash
sudo yum update -y
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd
```

### 🌐 Add Sample HTML Page

```bash
echo "Welcome to EC2 Backup Page" | sudo tee /var/www/html/index.html
```

Check in browser → `http://<EC2-PUBLIC-IP>`
✅ You should see: *Welcome to EC2 Backup Page*

---

## ✅ 3. Create and Insert Data in RDS

### 📦 Install MySQL Client (on EC2)

```bash
sudo yum install mysql -y
```

### 🔌 Connect to RDS

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

Enter password.

### 🗃️ Create DB and Insert Sample Data

```sql
CREATE DATABASE backupdb;
USE backupdb;

CREATE TABLE testdata (
id INT PRIMARY KEY,
name VARCHAR(50)
);

INSERT INTO testdata VALUES (1, 'EC2 Backup'), (2, 'RDS Backup');

SELECT * FROM testdata;
```

✅ Output should show **two rows inserted**.

---

## ✅ 4. Set Up AWS Backup

### 🔸 Create Backup Vault (Optional)

* Go to **AWS Backup > Backup vaults > Create vault**
* Encryption: Default AWS KMS key
* Click **Create**

---

### 🔸 Create Backup Plan

* Go to **AWS Backup > Backup Plans**
* Click **Create Backup Plan**
* Choose **Build a new plan**
* Plan Name: `EC2-RDS-BackupPlan`
* Add Backup Rule:

  * Rule name: `DailyBackup`
  * Vault: `MyBackupVault` (or Default)
  * Frequency: **Daily**
  * Retention: **7 days**
  * Backup window: Default

---

### 🔸 Assign EC2 and RDS Resources

* Go to the plan → **Assign Resources**
* Assignment name: `EC2-RDS-Assign`
* IAM role: Use AWS default
* Assign by **Resource ID**
* Select:

  * Your EC2 instance
  * Your RDS instance
* Click **Assign resources**

---

## ✅ 5. Trigger Backup and Validate

### 🔹 Start On-Demand Backup

* Go to **Backup Plans**
* Select `EC2-RDS-BackupPlan`
* Click **Actions > Start on-demand backup**
* Choose both EC2 and RDS resources
* Click **Start Backup**

✅ Monitor the backup under **Jobs**

---

## ✅ 6. View Recovery Points

* Go to **Backup Vaults**
* Select your vault (e.g., `MyBackupVault`)
* Check **Recovery Points**
* You should see entries for:

  * EC2 instance
  * RDS database
    ✅ Status: **Completed**

---

## 🔹 Conclusion

This project ensures **regular, automated backups** for **EC2 and RDS** resources using **AWS Backup**, allowing for **simple recovery in case of data loss or failure**.

---

👤 **Author:** *Krushna Nangare*
💼 **LinkedIn:** [Your LinkedIn Profile](https://linkedin.com/in/krushna-nangare)

---

