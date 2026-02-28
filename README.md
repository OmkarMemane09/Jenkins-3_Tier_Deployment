#  EasyCRUD3-Tier CI/CD Project

This project demonstrates a complete CI/CD pipeline using **Jenkins**, **Docker**, **MariaDB**, and **AWS EC2** to build, push, and deploy a full-stack application.

---

##  System Architecture

The deployment follows a 3-tier logical structure hosted on a single AWS EC2 node:



* **Frontend Tier:** React/Vite (Docker Container - Port 80)
* **Backend Tier:** Spring Boot (Docker Container - Port 8081)
* **Database Tier:** MariaDB (Linux Service - Port 3306)
* **Automation:** Jenkins Pipeline (CI/CD)

---

## Prerequisites & Infrastructure

### 1. AWS EC2 Setup
Ensure your Security Group allows the following inbound ports:
| Service | Port | Protocol |
| :--- | :--- | :--- |
| **HTTP (Frontend)** | 80 | TCP |
| **API (Backend)** | 8081 | TCP |
| **MariaDB** | 3306 | TCP |
| **Jenkins** | 8080 | TCP |
| **SSH** | 22 | TCP |

### 2. Software Installation (EC2 Shell)

**Install Java & Jenkins:**
```bash
sudo apt update && sudo apt install openjdk-17-jdk -y

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc [https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key](https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key)
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] [https://pkg.jenkins.io/debian-stable](https://pkg.jenkins.io/debian-stable) binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update && sudo apt-get install jenkins -y
```
### Install Docker & Permissions:

```Bash
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
# Grant Jenkins sudo privileges for deployment scripts
echo "jenkins ALL=(ALL) NOPASSWD: ALL" | sudo tee -a /etc/sudoers
```
### Install MariaDB:

```Bash
sudo apt install mariadb-server -y
sudo mysql_secure_installation
```
### Database Setup
Log into MariaDB and create the schema:
```bash
mysql -h dburl -u admin -p
```
```bash
CREATE DATABASE student_db;

GRANT ALL PRIVILEGES ON springbackend.* TO 'username'@'localhost' IDENTIFIED BY 'your_password';

USE student_db;

CREATE TABLE `students` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) DEFAULT NULL,
  `email` varchar(255) DEFAULT NULL,
  `course` varchar(255) DEFAULT NULL,
  `student_class` varchar(255) DEFAULT NULL,
  `percentage` double DEFAULT NULL,
  `branch` varchar(255) DEFAULT NULL,
  `mobile_number` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=80 DEFAULT CHARSET=latin1 COLLATE=latin1_swedish_ci;

exit;

```

### CI/CD Pipeline Workflow
The automation is handled by a Jenkins Declarative Pipeline.

**1. Jenkins Credentials**
 - Go to Manage Jenkins -> Credentials and add:

 - ID: dockerhub-cred

 - Username: <your-dockerhub-username>

 - Password: <your-dockerhub-password>

**2. Pipeline Stages**
 - Clone Repo: Pulls code from GitHub.

 - Build Images: Builds Frontend and Backend Docker images.

 - Docker Hub Push: Logs in and pushes images to the registry.

 - Deploy: Kills existing containers and runs updated versions on EC2.

## Application Configuration
Make sure that u changes application.properties in this repo
```bash
spring.datasource.url=jdbc:mysql://DB_URL:3306/student_db
spring.datasource.username=root
spring.datasource.password=redhat
```

#### Frontend (.env)
```Bash
VITE_API_URL="http://<YOUR_EC2_IP>:8081/api"
```
