#  Nginx for Node.js App

This project sets up an Nginx reverse proxy with load balancing for a Node.js web application. It includes Dockerization and securing the connection with a self-signed TLS certificate.

##  Installation Steps

### 1️⃣ Install Nginx on Linux
```sh
sudo apt update && sudo apt install nginx -y
```
<button onclick="navigator.clipboard.writeText('sudo apt update && sudo apt install nginx -y')">📋 Copy</button>

### 2️⃣ Clone the Repository
```sh
git clone https://github.com/Devisrisamidurai/Nginx-node.js-app.git
cd Nginx-node.js-app
```
<button onclick="navigator.clipboard.writeText('git clone https://github.com/Devisrisamidurai/Nginx-node.js-app.git && cd Nginx-node.js-app')">📋 Copy</button>

### 3️⃣ Dockerize the Node.js Application 🐳
Build and run the Node.js app inside Docker containers:
```sh
docker build -t node-app .
docker run -d -p 3001:3000 --name app1 node-app
docker run -d -p 3002:3000 --name app2 node-app
docker run -d -p 3003:3000 --name app3 node-app
```
<button onclick="navigator.clipboard.writeText('docker build -t node-app . && docker run -d -p 3001:3000 --name app1 node-app && docker run -d -p 3002:3000 --name app2 node-app && docker run -d -p 3003:3000 --name app3 node-app')">📋 Copy</button>

### 4️⃣ Configure Nginx as Reverse Proxy with Load Balancing 🔄
Edit the Nginx configuration file:
```sh
sudo vi /etc/nginx/nginx.conf
```
<button onclick="navigator.clipboard.writeText('sudo nano /etc/nginx/nginx.conf')">📋 Copy</button>

Paste the following configuration:
```nginx
worker_processes 1;

events {
    worker_connections 1024;
}

http {
    include mime.types;

    upstream nodejs_cluster {
        server 127.0.0.1:3001;
        server 127.0.0.1:3002;
        server 127.0.0.1:3003;
    }

    server {
        listen 443 ssl;
        server_name localhost;

        ssl_certificate /etc/nginx/ssl/nginx-selfsigned.crt;
        ssl_certificate_key /etc/nginx/ssl/nginx-selfsigned.key;

        location / {
            proxy_pass http://nodejs_cluster;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }

    server {
        listen 80;
        server_name localhost;

        location / {
            return 301 https://$host$request_uri;
        }
    }
}
```

Restart Nginx:
```sh
sudo systemctl restart nginx
```
<button onclick="navigator.clipboard.writeText('sudo systemctl restart nginx')">📋 Copy</button>

### 5️⃣ Secure Connection with Self-Signed TLS Certificate 🔐
Create a directory for SSL certificates:
```sh
sudo mkdir /etc/nginx/ssl
cd /etc/nginx/ssl
```
<button onclick="navigator.clipboard.writeText('sudo mkdir /etc/nginx/ssl && cd /etc/nginx/ssl')">📋 Copy</button>

Generate a self-signed certificate:
```sh
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout nginx-selfsigned.key -out nginx-selfsigned.crt
```
<button onclick="navigator.clipboard.writeText('sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout nginx-selfsigned.key -out nginx-selfsigned.crt')">📋 Copy</button>

Restart Nginx to apply changes:
```sh
sudo systemctl restart nginx
```
<button onclick="navigator.clipboard.writeText('sudo systemctl restart nginx')">📋 Copy</button>

### 6️⃣ Verify Setup ✅
Check if Nginx is running:
```sh
sudo systemctl status nginx
```
<button onclick="navigator.clipboard.writeText('sudo systemctl status nginx')">📋 Copy</button>

Test HTTPS access in the browser:
```
https://localhost
```
If using a self-signed certificate, accept the warning.

### 🎯 Conclusion
You have successfully set up an Nginx reverse proxy with load balancing for a Dockerized Node.js application and secured it with HTTPS.

