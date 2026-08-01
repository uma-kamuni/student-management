# Student Management System

## Prerequisites 

1. **Create Database in AWS RDS - mysql**
   ```bash
   username: admin
   password: admin123
   After creating DB you'll get endpoint.
   ```


2. **Launch EC2 Instance**
   ```bash
   Type:c7i.flex-large
   Storage: 15gb
   Security Group: ports(80,8080,22,443)
   Login to EC2:
   git clone https://github.com/uma-kamuni/student-management.git
   ```

3. **Java-jdk-21**
   ```bash
   sudo apt update
   sudo apt install -y openjdk-21-jdk
   java --version

   If you want to switch/set to perticular java Version, command is
   update-alternatives --config java
   java --version
   ```

4. **Maven**
   ```bash
   sudo apt install -y maven
   mvn -version
   ```

5. **MySQL Client**
   ```bash
   ## step 1-
   sudo apt install mysql-client -y
   ```

    ## Step 2 — Configure the app's DB connection

Edit `src/main/resources/db.properties`:

```properties
db.url=jdbc:mysql://localhost:3306/school_db?useSSL=false&serverTimezone=UTC
db.user=root
db.password=YOUR_MYSQL_PASSWORD
```

    mysql -u admin -h database-1-instance-1.c38m4q40gefi.eu-north-1.rds.amazonaws.com -padmin123
    show databases;
    create database school_db;
    use school_db;
    CREATE DATABASE IF NOT EXISTS school_db;
    USE school_db;

    CREATE TABLE IF NOT EXISTS students (
    id     INT AUTO_INCREMENT PRIMARY KEY,
    name   VARCHAR(100)  NOT NULL,
    email  VARCHAR(100)  NOT NULL,
    course VARCHAR(100)  NOT NULL,
    marks  DECIMAL(5,2)  NOT NULL DEFAULT 0
    );
    select * from student;
    exit
    ```
6. Build the WAR with Maven

From the project root (where `pom.xml` lives):

```bash
cd student-management/
mvn clean package
```
Maven will compile everything and produce:

```
target/student-management.war
```

That single file is the whole application.
   
7. **Download Apache Tomcat 10 or 11**
Google->Browse/search for install tomcat
select tomcat 10 --> copy link address which is in .tar.gz
```bash
   wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.57/bin/apache-tomcat-10.1.57.tar.gz
   tar -xvzf apache-tomcat-10.1.57.tar.gz
   rm apache-tomcat-10.1.57.tar.gz
   cd apache-tomcat-10.1.57/
   cd bin/
   ./catalina.sh start  -->   browse Ec2Pub_ip:8080

   ##Copy the WAR into Tomcat's `webapps` folder, then start Tomcat:##
   cp student-management/target/student-management.war  apache-tomcat-10.1.57/webapps/student.war
   cd apache-tomcat-10.1.57/bin/
   ./catalina.sh start   --> Ec2Pub_ip:8080/student

```
```
Login to database
mysql -u admin -h database-1-instance-1.c38m4q40gefi.eu-north-1.rds.amazonaws.com -padmin123
Check data is adding or not.
```
______________________________________________________________________________________________________________________________
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/d26677bc-5632-42da-b0c4-37969efc9c82" />

```
Here ,we are on http://Ec2_pub_ip:8080/student    
where port & ip is exposed
To hide port , we use Nginx Reverse Proxy
And to hide IP ,configure domain
```
______________________________________________________________________________________________________________________________

8. **Nginx**
   ```bash
   apt install nginx -y
   systemctl status nginx
   cd /etc/nginx
   cat proxy_params
   cd sites-available/
   
   vim student

    server {
        listen 80;
        location / {
                proxy_pass http://localhost:8080;
                include proxy_params;
        }
    }


    rm -rf default
    nginx -t
    ln -s /etc/nginx/sites-available/student /etc/nginx/sites-enabled/
    systemctl reload nginx
    curl localhost
   ```

    Browse: Ec2_pub_ip/student
    <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/9435a4ef-c9eb-4384-85ee-beaad8dc4a1e" />

    We r still on http to get https install certbot and add domain to hide ip

    
9. **Create Database in AWS RDS - mysql**
   ```bash
    apt install certbot python3-certbot-nginx -y
    certbot --nginx
    |__> add email
    |__>Y
    |__>Y
    |__>To apply to both domain just enter directly

    Now check student file in nginx sites available it will add certificate
    cd /etc/nginx/sites-available/
    
    vim student
    server {
        listen 80;
        server_name umak.online;

        location / {
                proxy_pass http://localhost:8080;
                include proxy_params;
        }
     }

     nginx -t
     systemctl reload nginx
   ```


   Browse : umak.online/student
    <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/20d2e986-ef75-485a-964f-5ae51b6781f9" />



   ```
