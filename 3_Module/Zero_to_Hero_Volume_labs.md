Docker Volumes Hands-On Labs

## Lab 1: Container Ephemeral Storage - Understand that container data is lost when the container is removed.

#### Run a container
 - docker run -it --name ephemeral ubuntu:22.04
 - echo "Temporary data" > /data.txt
 - cat /data.txt
 - exit

#### Remove container
 - docker rm ephemeral

Observation: Any files inside the container are lost.
Takeaway: Containers are ephemeral. Critical data must be stored outside.

---

## Lab 2: Named Docker Volumes - Persist data across containers using named volumes.

#### Create a named volume
 - docker volume create myvolume

#### Start a container with the volume
 - docker run -it --name volume-container -v myvolume:/app ubuntu:22.04
 - echo "Persistent data" > /app/data.txt
 - exit

#### Start a new container using the same volume
 - docker run -it --name new-container -v myvolume:/app ubuntu:22.04
 - cat /app/data.txt

Takeaway: Named volumes are persistent and portable across containers.

---

## Lab 3: Anonymous Volumes - Understand anonymous volumes.

 - docker run -it -v /app ubuntu:22.04
 - echo "Anonymous volume data" > /app/data.txt
 - exit

 - docker volume ls  # Docker auto-created an anonymous volume

Takeaway: Anonymous volumes are temporary and harder to manage.

---

## Lab 4: Bind Mounts vs Volumes vs tmpfs -  Compare different types of container storage.

#### Bind mount (maps host directory)
 - mkdir -p ~/hostdata
 - docker run -it -v ~/hostdata:/app ubuntu:22.04
 - echo "Host bind mount" > /app/data.txt
 - exit

#### tmpfs (memory only)
 - docker run -it --tmpfs /app ubuntu:22.04
 - echo "tmpfs memory data" > /app/data.txt
 - exit

#### Named Docker volume
 - docker volume create prodvolume
 - docker run -it -v prodvolume:/app ubuntu:22.04
 - echo "Docker volume data" > /app/data.txt
 - exit

Takeaways:
 - Bind mount: Flexible, not portable.
 - tmpfs: Fast, ephemeral, lost on stop.
 - Docker volume: Managed by Docker, persistent, production-ready.

---

#### Lab 5: Real Application – Flask + Persistent Data - Apply Docker volumes with a real app.

Directory structure:
flask-app/
 ├─ app.py
 └─ requirements.txt
 
app.py:
```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    with open("/data/data.txt", "a") as f:
        f.write("Hello Docker!\n")
    return "Data written!"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```
requirements.txt:
```
     flask
```
Dockerfile:
```
     FROM python:3.12-slim
     WORKDIR /app
     COPY requirements.txt .
     RUN pip install --no-cache-dir -r requirements.txt
     COPY . .
     VOLUME /data
     CMD ["python", "app.py"]
```
Commands:
 - docker build -t flask-app:latest .
 - docker run -d -p 5000:5000 -v flaskdata:/data flask-app:latest
 - curl http://localhost:5000/
 - docker exec -it <container_id> cat /data/data.txt

Takeaway: Persistent data survives container restarts.

---

## Lab 6: Multi-Container Volume Sharing - Share data between containers.

#### Writer container
docker run -it --name writer -v shared:/data ubuntu:22.04
echo "Shared between containers" > /data/shared.txt
exit

#### Reader container
docker run -it --name reader -v shared:/data ubuntu:22.04
cat /data/shared.txt

Takeaway: Volumes allow multiple containers to share data safely.

---

## Lab 7: Backup and Restore Volumes - Learn how to backup and restore volumes.

#### Backup
docker run --rm -v flaskdata:/data -v $(pwd):/backup ubuntu \
    tar cvf /backup/flaskdata_backup.tar /data

#### Restore
docker run --rm -v flaskdata:/data -v $(pwd):/backup ubuntu \
    bash -c "cd /data && tar xvf /backup/flaskdata_backup.tar --strip 1"

Takeaway: Volumes can be backed up, migrated, and restored without touching containers.

---

## Lab 8: Production Best Practices
 - Use named volumes for DBs, logs, and configs.
 - Avoid bind mounts for production apps.
 - Use tmpfs for ephemeral cache or session data.
 - Back up volumes regularly using tar or cloud snapshots.
 - Share volumes only between containers that need consistent data.

Pro Tip: On AWS, attach EBS/EFS volumes as Docker volumes for resilience in production.
