# 7️⃣ How to set up a maintenance page or temporary site downtime?

---

## 🧩 Problem Description

You want to:

- Deploy a new version of your site
- Take the backend down temporarily
- Show users a "We'll be back soon" message without breaking Nginx
- Optionally allow your own IP to bypass maintenance

✅ Nginx can serve a static maintenance page while bypassing upstreams entirely.

---

## 📚 Core Nginx Concepts

- `return` – serve a response directly
- `error_page` – custom response for errors like 503
- `allow` / `deny` – IP-based access control
- `try_files` – fallback to maintenance.html

---

## ⚙️ Method 1: Serve Maintenance Page for All Requests

```nginx
server {
    listen 80;
    server_name mysite.com;

    root /var/www/html;

    location / {
        return 503;
    }

    error_page 503 @maintenance;

    location @maintenance {
        rewrite ^(.*)$ /maintenance.html break;
    }

    location = /maintenance.html {
        internal;
    }
}
```

💡 Explanation

- All requests return HTTP 503 (Service Unavailable)
- The error is intercepted and redirected to /maintenance.html
- internal means /maintenance.html cannot be accessed directly via URL

---

## ⚙️ Method 2: Allow Whitelist IP to Bypass

```nginx
location / {
    allow 123.123.123.123;  # your IP
    deny all;

    proxy_pass http://backend;
}
```

Or use it with the 503 config:

```nginx
location / {
    allow 123.123.123.123;
    deny all;

    proxy_pass http://backend;
    error_page 403 =503;
}
```

- Your IP sees the real site
- Everyone else sees the maintenance page

---

## ⚙️ Method 3: Maintenance Toggle via File

Use a file presence to switch maintenance mode on/off:

```nginx
location / {
    if (-f /etc/nginx/maintenance.flag) {
        return 503;
    }

    proxy_pass http://backend;
}
```

🔄 Enable / Disable:

```bash
# Enable maintenance
touch /etc/nginx/maintenance.flag

# Disable maintenance
rm /etc/nginx/maintenance.flag
```

> ⚠️ Using if in Nginx is generally discouraged, but safe in this simple context.

---

## 🛠️ Real-World Tips

| Task             | Tip                                                             |
| ---------------- | --------------------------------------------------------------- |
| Styling the page | Use a simple `/maintenance.html` with inline CSS                |
| SEO impact       | Return 503 instead of 200 — tells search engines it's temporary |
| Automation       | Add flag toggle to your deployment script                       |
| Secure preview   | Allow access from VPN IPs or admin subnet only                  |

---

## 🧪 Test It!

- Try visiting from allowed and non-allowed IPs
- Use curl to check response code:

```bash
curl -I http://mysite.com
```

You should see:

```bash
HTTP/1.1 503 Service Temporarily Unavailable
```

---

## ✅ Summary

- Use return 503 and error_page to serve a maintenance page
- Use IP whitelisting to allow internal access
- Use a flag file to toggle maintenance mode programmatically
- Always return 503, not 200, to indicate temporary downtime

---
