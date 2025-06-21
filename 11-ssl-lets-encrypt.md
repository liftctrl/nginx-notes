# 1️⃣1️⃣ How to set up SSL with Let’s Encrypt and auto-renew?

---

## 🧩 Problem Description

You want to:

- Secure your site with HTTPS to encrypt traffic
- Use a free, trusted SSL certificate
- Automate the certificate renewal process

✅ Let’s Encrypt provides free SSL certificates, and Nginx can easily integrate with it for automated SSL management.

---

## 📚 Core Nginx Concepts

- `ssl_certificate` – path to your SSL certificate
- `ssl_certificate_key` – path to your SSL private key
- `ssl_dhparam` – optional: for stronger Diffie-Hellman key exchange
- `ssl_session_cache` – improves connection speed by caching SSL sessions
- `letsencrypt` – service for free SSL certificates

---

## ⚙️ Step-by-Step: Setting Up Let’s Encrypt SSL

### 1. Install Certbot

First, install Certbot, the official Let’s Encrypt client.

#### For Ubuntu/Debian:

```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

#### For CentOS:

```bash
sudo yum install epel-release
sudo yum install certbot python3-certbot-nginx
```

### 2. Obtain SSL Certificate

Run Certbot to automatically configure SSL for Nginx:

```bash
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Certbot will:

- Verify your domain
- Request an SSL certificate from Let’s Encrypt
- Automatically configure your Nginx server with the appropriate SSL settings

> During the process, you'll be asked to choose whether to redirect all traffic to HTTPS.

### 3. Update Nginx Configuration (Optional)

Once the SSL certificate is installed, Certbot will automatically update your Nginx configuration. It typically looks like this:

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    listen 443 ssl;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://backend;
    }
}
```

- ssl_certificate and ssl_certificate_key point to the generated SSL files
- ssl_dhparam (optional) strengthens key exchange security
- ssl_session_cache optimizes SSL handshake performance

---

## ⚙️ Step 2: Automate Certificate Renewal

Let’s Encrypt certificates are valid for 90 days, so automatic renewal is essential.

### 1. Test Renewal

Run this command to simulate a certificate renewal process:

```bash
sudo certbot renew --dry-run
```

### 2. Set Up Cron Job (Automatic Renewal)

Certbot automatically installs a cron job or systemd timer for automatic renewals. You can verify by running:

```bash
sudo systemctl list-timers
```

If the timer exists, you're all set! Certbot will attempt to renew your certificates every 12 hours and reload Nginx if successful.

If you need to add your own cron job, use:

```bash
sudo crontab -e
```

Add the following line for daily certificate renewal:

```bash
0 3 * * * certbot renew --quiet && systemctl reload nginx
```

> This renews the certificate at 3:00 AM every day and reloads Nginx.

---

## 🧪 Test It!

Once everything is set up, visit your site in a browser:

- Make sure the connection is secure (look for the padlock icon in the address bar)
- Verify the certificate details to ensure it's issued by Let’s Encrypt

You can also test with curl:

```bash
curl -I https://yourdomain.com
```

You should see HTTP/2 200 OK and a Strict-Transport-Security header indicating your site is served over HTTPS.

---

## ⚠️ Common Issues

| Problem                            | Solution                                                     |
| ---------------------------------- | ------------------------------------------------------------ |
| “SSL certificate expired”          | Run `certbot renew` manually if auto-renewal fails           |
| Mixed content warning (HTTP/HTTPS) | Ensure all internal resources (JS, CSS, etc.) use HTTPS URLs |
| Let’s Encrypt rate limits          | If you hit the limits, wait 7 days for renewal attempts      |

---

## ✅ Summary

- Use Certbot to easily install and configure Let’s Encrypt SSL certificates
- Automate renewal with cron or systemd
- Secure your site with HTTPS and improve trust and security
- Let’s Encrypt certificates are free, but expire every 90 days

---
