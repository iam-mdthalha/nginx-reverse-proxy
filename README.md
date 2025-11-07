# 🚀 NGINX Reverse Proxy for Node.js

This project is a complete end-to-end DevOps implementation of NGINX as a reverse proxy for Node.js applications.  
It demonstrates how to set up a production-ready architecture with Node.js backend, NGINX reverse proxy, static file serving, and load balancing capabilities.

---

## 📌 Project Overview

- Node.js Express backend application  
- NGINX configured as reverse proxy  
- Static frontend served by NGINX  
- API requests proxied to Node.js backend  
- Production-ready NGINX configuration  
- WebSocket support (Socket.io)  
- CORS enabled for cross-origin requests  
- Deployed on Ubuntu/EC2  
- Optional HTTPS using Certbot (Free SSL)  
- Fully documented setup and deployment process  

---

## 📂 Repository Structure

```
nginx-node-proxy/
│
├── backend/
│     ├── index.js              # Node.js Express server
│     ├── package.json
│     └── node_modules/
│
├── frontend/
│     ├── index.html
│     ├── css/
│     ├── js/
│     └── images/
│
├── nginx/
│     └── nginx-proxy.conf      # NGINX reverse proxy config
│
└── README.md
```

---

## ✅ Features Implemented

- Node.js Express API server  
- NGINX reverse proxy configuration  
- Static file serving via NGINX  
- API route proxying to backend  
- WebSocket support for real-time features  
- CORS configuration  
- Production-grade server setup  
- SSL/TLS support  
- Load balancing ready architecture  

---

## 🧱 Architecture (High Level)

1. Client makes request to NGINX (port 80/443)  
2. NGINX serves static files (HTML/CSS/JS) directly  
3. API requests (`/api/*`) proxied to Node.js backend (port 3000)  
4. Node.js processes request and returns response  
5. NGINX forwards response back to client  
6. WebSocket connections upgraded and proxied to backend  
7. Static assets cached by NGINX for performance  

---

## 🎯 Why Use NGINX with Node.js?

- **Performance**: NGINX handles static files faster than Node.js  
- **SPA Support**: Fallback routing for React, Vue, Angular applications  
- **Reverse Proxy**: Route API requests to Node.js backend  
- **WebSocket Support**: Proxy Socket.io connections seamlessly  
- **SSL Termination**: Offload SSL/TLS encryption from Node.js  
- **Security**: Additional layer of security and DDoS protection  
- **Scalability**: Easy to add load balancing when needed  

---

## 📦 How to Use This Repository

Below are the commands to clone, install, configure, deploy, and maintain the project:

### ✅ 1. Clone the Repository

```bash
git clone https://github.com/<your-username>/nginx-node-proxy.git
cd nginx-node-proxy
```

---

## 🔧 Backend Setup (Node.js)

### ✅ 2. Install Node.js and npm

```bash
# Update package list
sudo apt update

# Install Node.js (NodeSource repository - recommended)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Verify installation
node -v
npm -v
```

### ✅ 3. Set Up Node.js Backend

```bash
# Navigate to backend directory
cd backend

# Initialize npm project (if not already done)
npm init -y

# Install required packages
npm install express cors socket.io
```

### ✅ 4. Run Node.js Backend

```bash
# Development mode (simple)
node index.js

# Production mode (with PM2 - recommended)
sudo npm install -g pm2
pm2 start index.js --name nodejs-backend
pm2 save
pm2 startup
```

### ✅ Check Backend Status

```bash
# If using PM2
pm2 status
pm2 logs nodejs-backend

# Test backend directly
curl http://localhost:3000/api/health
```

---

## 🌐 Frontend & NGINX Setup

### ✅ 6. Install NGINX

```bash
sudo apt update
sudo apt install nginx -y
```

### ✅ 7. Copy Frontend Files to Web Root

```bash
sudo mkdir -p /var/www/frontend
sudo cp -r frontend/* /var/www/frontend/
```

### ✅ 8. Set Proper Permissions

```bash
sudo chown -R www-data:www-data /var/www/frontend
sudo chmod -R 755 /var/www/frontend
```

### ✅ 9. Deploy NGINX Reverse Proxy Configuration

```bash
sudo cp nginx/nginx-proxy.conf /etc/nginx/sites-available/nginx-proxy
sudo ln -s /etc/nginx/sites-available/nginx-proxy /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
```

### ✅ 10. Example NGINX Configuration (`nginx-proxy.conf`)

```nginx
server {
    listen 80;
    server_name <YOUR-EC2-IP>;
    
    # Root directory for frontend static files
    root /var/www/frontend;
    index index.html;
    
    # ✅ SPA support (React, Vue, Angular)
    # Serve static files or fallback to index.html for client-side routing
    location / {
        try_files $uri $uri/ /index.html;
    }
    
    # ✅ Reverse proxy to Node.js API
    location /api/ {
        proxy_pass http://<YOUR-EC2-IP>:3000;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    
    # ✅ WebSocket support (Socket.io)
    location /socket.io/ {
        proxy_pass http://<YOUR-EC2-IP>:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```

### ✅ 11. Test & Reload NGINX

```bash
# Test configuration
sudo nginx -t

# Reload NGINX
sudo systemctl reload nginx

# Check status
sudo systemctl status nginx
```

---

## 🔍 Testing the Setup

### ✅ 12. Access the Application

```bash
# Frontend (served by NGINX)
http://<YOUR-EC2-IP>

# Backend API (proxied through NGINX)
http://<YOUR-EC2-IP>/api/health
http://<YOUR-EC2-IP>/api/data

# Test with curl
curl http://<YOUR-EC2-IP>/api/health
curl -X POST http://<YOUR-EC2-IP>/api/data \
  -H "Content-Type: application/json" \
  -d '{"test": "data"}'
```

---

## 🔒 Optional: Enable HTTPS (SSL/TLS)

### ✅ 13. Install Certbot

```bash
sudo apt install certbot python3-certbot-nginx -y
```

### ✅ 14. Obtain SSL Certificate

```bash
# Replace with your actual domain
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

### ✅ 15. Auto-renewal Test

```bash
sudo certbot renew --dry-run
```

---

## 🔄 Update Workflow

### ✅ 16. Update Backend Code

```bash
cd backend
git pull
npm install
pm2 restart nodejs-backend
```

### ✅ 17. Update Frontend Code

```bash
git pull
sudo cp -r frontend/* /var/www/frontend/
sudo systemctl reload nginx
```

### ✅ 18. Update NGINX Configuration

```bash
sudo cp nginx/nginx-proxy.conf /etc/nginx/sites-available/nginx-proxy
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🛠 Useful Commands

### Node.js & PM2 Commands

```bash
# View PM2 logs
pm2 logs nodejs-backend

# Restart Node.js app
pm2 restart nodejs-backend

# Stop Node.js app
pm2 stop nodejs-backend

# Delete from PM2
pm2 delete nodejs-backend

# Monitor resources
pm2 monit

# List all processes
pm2 list
```

### NGINX Commands

```bash
# View error logs
sudo tail -f /var/log/nginx/error.log

# View access logs
sudo tail -f /var/log/nginx/access.log

# Test configuration
sudo nginx -t

# Restart NGINX
sudo systemctl restart nginx

# Stop NGINX
sudo systemctl stop nginx

# Start NGINX
sudo systemctl start nginx

# Check NGINX version
nginx -v
```

### System & Network Commands

```bash
# Check which process is using port 3000
sudo lsof -i :3000

# Check all listening ports
sudo netstat -tulpn

# Check process list
ps aux | grep node
ps aux | grep nginx

# Monitor system resources
htop
```

---

## 🚨 Troubleshooting

### Backend Issues

```bash
# If Node.js app crashes
pm2 logs nodejs-backend
pm2 restart nodejs-backend

# If port 3000 is already in use
sudo lsof -i :3000
sudo kill -9 <PID>

# Check Node.js version
node -v
npm -v
```

### NGINX Issues

```bash
# If NGINX fails to start
sudo nginx -t
sudo systemctl status nginx
sudo tail -f /var/log/nginx/error.log

# If configuration syntax error
sudo nginx -t

# Reset permissions
sudo chown -R www-data:www-data /var/www/frontend
sudo chmod -R 755 /var/www/frontend
```

### Firewall Issues

```bash
# Allow HTTP and HTTPS
sudo ufw allow 'Nginx Full'
sudo ufw status

# For AWS EC2, ensure Security Group allows:
# - Port 80 (HTTP)
# - Port 443 (HTTPS)
# - Port 22 (SSH)
```

---

Then start multiple Node.js instances:

```bash
PORT=3000 pm2 start index.js --name backend-1
PORT=3001 pm2 start index.js --name backend-2
PORT=3002 pm2 start index.js --name backend-3
```

---

## 🎯 Project Goal

To demonstrate a production-ready DevOps workflow using:

- Node.js & Express  
- NGINX Reverse Proxy  
- WebSocket (Socket.io)   
- Ubuntu / EC2  
- Git & GitHub  

Perfect for DevOps portfolios, resumes, and interviews.

---

## 👤 Author

**MOHAMMED THALHA**  
DevOps | AWS | Node.js | NGINX | CI/CD

---

## ⭐ Support

If you found this project helpful, please ⭐ the repository!
