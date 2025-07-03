## 🔐 **Let’s Encrypt SSL with Certbot Guide**  

### ✅ **What is Let’s Encrypt & Certbot?**  
**Let’s Encrypt** is a free, automated, and open **Certificate Authority (CA)** that provides SSL/TLS certificates.  
**Certbot** is an official client that simplifies obtaining and renewing Let’s Encrypt certificates.  

✔ **Free SSL certificates** (valid for 90 days, auto-renewable)  
✔ **Supports HTTP & DNS challenges** (for wildcard certs)  
✔ **Works with Apache, Nginx, Caddy, and more**  

---

## 🛠️ **Step 1: Install Certbot**  

### **Debian/Ubuntu**  
```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx  # For Nginx
sudo apt install certbot python3-certbot-apache # For Apache
```

### **RHEL/CentOS**  
```bash
sudo dnf install certbot python3-certbot-nginx
```

### **Arch Linux**  
```bash
sudo pacman -S certbot certbot-nginx
```

### **macOS (Homebrew)**  
```bash
brew install certbot
```

---

## 🔐 **Step 2: Obtain an SSL Certificate**  

### **Option 1: Automatic Setup (Nginx/Apache)**  
```bash
sudo certbot --nginx   # For Nginx
sudo certbot --apache  # For Apache
```
Certbot will:  
✔ Automatically detect your domains  
✔ Configure SSL in your web server  
✔ Set up auto-renewal  

### **Option 2: Manual Setup (Any Web Server)**  
```bash
sudo certbot certonly --standalone -d example.com -d www.example.com
```
Files will be saved in:  
- **Certificate**: `/etc/letsencrypt/live/example.com/fullchain.pem`  
- **Private Key**: `/etc/letsencrypt/live/example.com/privkey.pem`  

### **Option 3: Wildcard Certificates (DNS Challenge)**  
```bash
sudo certbot certonly --manual --preferred-challenges=dns -d *.example.com
```
You’ll need to manually add a **TXT DNS record** when prompted.  

---

## ⚙️ **Step 3: Configure Web Server**  

### **Nginx Example**  
```nginx
server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    location / {
        root /var/www/html;
    }
}
```

### **Apache Example**  
```apache
<VirtualHost *:443>
    ServerName example.com
    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/example.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
    DocumentRoot /var/www/html
</VirtualHost>
```

### **Caddy (Automatic HTTPS - No Certbot Needed)**  
```plaintext
example.com {
    root * /var/www/html
    file_server
}
```
Caddy **automatically** fetches and renews Let’s Encrypt certs.  

---

## 🔄 **Step 4: Auto-Renew Certificates**  

Let’s Encrypt certificates expire every **90 days**, but Certbot can auto-renew them.  

### **Test Renewal (Dry Run)**  
```bash
sudo certbot renew --dry-run
```

### **Set Up Cron Job (Auto-Renewal)**  
```bash
sudo crontab -e
```
Add:  
```cron
0 3 * * * /usr/bin/certbot renew --quiet
```
This checks for renewal **daily at 3 AM** and renews if needed.  

---

## 🚀 **Step 5: Force HTTPS (Optional)**  

### **Nginx Redirect HTTP → HTTPS**  
```nginx
server {
    listen 80;
    server_name example.com;
    return 301 https://$host$request_uri;
}
```

### **Apache Redirect HTTP → HTTPS**  
```apache
<VirtualHost *:80>
    ServerName example.com
    Redirect permanent / https://example.com/
</VirtualHost>
```

---

## 📚 **Additional Resources**  
- **Certbot Docs**: [https://certbot.eff.org/](https://certbot.eff.org/)  
- **Let’s Encrypt Docs**: [https://letsencrypt.org/docs/](https://letsencrypt.org/docs/)  
- **Wildcard Cert Guide**: [https://certbot.eff.org/instructions](https://certbot.eff.org/instructions)  
