# 1️⃣3️⃣ How to configure automatic redirects from HTTP to HTTPS?

---

## 🧩 Problem Description

You want to:

- Ensure that all traffic to your site is encrypted using HTTPS
- Automatically redirect users from HTTP to HTTPS
- Prevent users from accidentally accessing the HTTP version of your site

✅ Nginx makes it simple to enforce HTTPS by redirecting all HTTP traffic to HTTPS, ensuring encrypted communication.

---

## 📚 Core Nginx Concepts

- `listen` – specifies which ports to listen on (80 for HTTP, 443 for HTTPS)
- `return` – sends an HTTP response with a specified status code
- `301 Moved Permanently` – the status code for permanent redirects

---

## ⚙️ Step-by-Step: Redirect HTTP to HTTPS

### 1. **Create HTTP to HTTPS Redirect Server Block**

In your Nginx configuration file, create a server block for HTTP (port 80) that will automatically redirect requests to HTTPS (port 443).

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    # Permanent redirect to HTTPS
    return 301 https://$host$request_uri;
}
```

💡 Explanation

- listen 80; listens for HTTP requests on port 80
- return 301 sends a permanent redirect response to HTTPS
- $host preserves the domain name, and $request_uri keeps the original request URI, so everything is redirected correctly
  - For example, http://yourdomain.com/page1 will be redirected to https://yourdomain.com/page1

### 2. Configure SSL (HTTPS) Server Block

You should already have an SSL configuration (e.g., from Let’s Encrypt). Here’s what a typical HTTPS server block might look like:

```nginx
server {
    listen 443 ssl http2;
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

💡 Explanation

- listen 443 ssl http2; enables SSL and HTTP/2 for secure traffic
- SSL certificate and key are configured via ssl_certificate and ssl_certificate_key
- Ensure all sensitive routes are accessed over HTTPS

---

## ⚠️ Common Issues and Tips

| Problem                         | Solution/Tip                                                                                      |
| ------------------------------- | ------------------------------------------------------------------------------------------------- |
| **Mixed content**               | Make sure all internal resources (images, JS, CSS) are loaded via HTTPS                           |
| **301 redirect causing issues** | Clear browser cache or use `curl` to test the redirect flow                                       |
| **Cache issues**                | Set cache headers for HTTP responses to avoid caching redirects (e.g., `Cache-Control: no-cache`) |
| **Incorrect server\_name**      | Make sure the `server_name` directive matches the actual domain                                   |

---

## 🧪 Test It!

1. Open your browser and try accessing your site via http://yourdomain.com
   - It should automatically redirect to https://yourdomain.com

2. Test using curl to see the redirect status:

```bash
curl -I http://yourdomain.com
```

You should see:

```http
HTTP/1.1 301 Moved Permanently
Location: https://yourdomain.com/
```

---

## ✅ Summary

- Redirect HTTP traffic to HTTPS to ensure secure connections using the 301 Moved Permanently response
- The Nginx return 301 https://$host$request_uri; directive ensures proper redirection
- SSL is required for the redirect to HTTPS
- Always check for mixed content or caching issues that might break the redirect

---
