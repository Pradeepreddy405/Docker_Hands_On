# Docker + MySQL Lab

This lab demonstrates how to run a MySQL container with Docker, including creating a persistent volume, initializing a database, and setting up a custom Docker network.

---

## 1. Create a Docker Network
```bash
docker network create app-net
```
- Creates a custom bridge network called `app-net`.  
- Allows multiple containers to communicate using container names instead of IPs.  

---

## 2. Run a MySQL Container with Volumes and Initialization Script
```bash
docker run -d --name mysql \
  --network app-net \
  -v mysql-data:/var/lib/mysql \
  -v /home/ubuntu/my-sql/init.sql:/docker-entrypoint-initdb.d/init.sql \
  -e MYSQL_ROOT_PASSWORD=root \
  mysql:5.7
```
- `-d` → Run container in detached mode (in the background).  
- `--name mysql` → Names the container `mysql`.  
- `--network app-net` → Connects the container to the custom network.  
- `-v mysql-data:/var/lib/mysql` → Persists MySQL data in a Docker volume.  
- `-v /home/ubuntu/my-sql/init.sql:/docker-entrypoint-initdb.d/init.sql` → Mounts an SQL initialization script so MySQL creates the database & tables on first run.  
- `-e MYSQL_ROOT_PASSWORD=root` → Sets the root password for MySQL.  
- `mysql:5.7` → Uses the official MySQL 5.7 image.  

---

## 3. Check Initialization Scripts Inside the Container
```bash
docker exec -it mysql ls /docker-entrypoint-initdb.d/
```
- `docker exec -it mysql` → Access the running container interactively.  
- `ls /docker-entrypoint-initdb.d/` → Lists all files in the folder where MySQL executes initialization scripts.  

---

## 4. Verify Databases Inside MySQL
```bash
docker exec -it mysql mysql -u root -proot -e "SHOW DATABASES;"
```
- Connects to MySQL inside the container as root using password `root`.  
- Executes the SQL command `SHOW DATABASES;` to list all databases.  

---

## ✅ Key Outcomes
- A persistent MySQL instance.  
- Automated database and table creation on first run.  
- Containerized isolation with a dedicated network.

