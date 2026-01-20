# 🐳 Nginx Website Hosting using Docker Volume (Persistent Storage)

This guide shows how to:
✅ Create a simple `index.html`  
✅ Create a Docker volume  
✅ Copy the `index.html` into the volume  
✅ Attach the volume to an Nginx container  
✅ Fix port conflict issues (`port is already allocated`)

---

## 📌 Prerequisites

- Ubuntu / WSL
- Docker installed
- Basic Linux commands

---

## 1️⃣ Create Project Folder

```bash
mkdir nginx-volume-demo
cd nginx-volume-demo
2️⃣ Create index.html
vim index.html
Paste this content:

<!DOCTYPE html>
<html>
<head>
  <title>Welcome Avishkar</title>
  <style>
    body { font-family: Arial; background:#0b1220; color:white; text-align:center; padding-top:120px; }
    h1 { font-size: 48px; }
    p { font-size: 22px; color: #cbd5e1; }
    .box { border: 2px solid #1e293b; display:inline-block; padding: 30px 50px; border-radius: 12px; }
  </style>
</head>
<body>
  <div class="box">
    <h1>🚀 Nginx Running in Docker</h1>
    <p>Hosted by: <b>Avishkar Avhad</b></p>
    <p>Docker Volume Attached ✅</p>
  </div>
</body>
</html>

3️⃣ Create Docker Volume
docker volume create nginx_data


Check volume:

docker volume ls

4️⃣ Copy index.html into Docker Volume

Run a temporary Alpine container to copy the file:

docker run --rm \
  -v nginx_data:/usr/share/nginx/html \
  -v $(pwd):/host \
  alpine sh -c "cp /host/index.html /usr/share/nginx/html/index.html"


✅ Now volume contains index.html

5️⃣ Run Nginx Container With Volume Attached
docker run -d \
  --name nginx-volume \
  -p 8080:80 \
  -v nginx_data:/usr/share/nginx/html \
  nginx:alpine

6️⃣ Access Website

Open in browser:

http://localhost:8080

7️⃣ Verify Volume Mounted (Optional)

Check inside container:

docker exec -it nginx-volume ls -l /usr/share/nginx/html

🛠️ Troubleshooting
❌ Error: port is already allocated

Example error:

Bind for 0.0.0.0:8080 failed: port is already allocated


✅ Fix 1: Stop and remove old container using same port

docker ps -a
docker stop nginx-site
docker rm nginx-site


✅ Fix 2: Remove created container (if stuck in "Created" status)

docker rm nginx-volume


✅ Then run container again:

docker run -d \
  --name nginx-volume \
  -p 8080:80 \
  -v nginx_data:/usr/share/nginx/html \
  nginx:alpine


✅ Fix 3: Use different port

docker run -d \
  --name nginx-volume \
  -p 8081:80 \
  -v nginx_data:/usr/share/nginx/html \
  nginx:alpine


Access:

http://localhost:8081

✅ Cleanup Commands (Optional)

Remove stopped containers:

docker container prune -f


Remove volume (⚠ deletes website files inside volume):

docker volume rm nginx_data
