# 3️⃣ How to enable HTTPS and redirect HTTP to HTTPS?

---

## 🧩 Problem Description

You’ve deployed a site using Nginx and notice:

- Your domain works with `http://`, but you want it to use `https://`
- Browsers show “Not Secure” warning
- You have an SSL certificate (Let’s Encrypt or otherwise) but don’t know how to configure it
- You want all `http://` traffic to automatically redirect to `https://`

✅ This is a common Nginx use case — and very important for production.

---

## 📚 Core Nginx Concepts

- `listen 443 ssl;` – enables HTTPS on port 443
- `ssl_certificate` & `ssl_certificate_key` – load your TLS certificate files
- `return 301 https://...` – permanently redirect HTTP to HTTPS

---

## ⚙️ Step 1: Redirect HTTP to HTTPS

Create an HTTP server block that **only handles redirection**:

```nginx
server {
    listen 80;
    server_name mysite.com www.mysite.com;

    return 301 https://$host$request_uri;
}
```

💡 Explanation

- All HTTP requests are caught here
- They’re redirected to the same $host and $request_uri, but with HTTPS

---

## ⚙️ Step 2: Enable HTTPS with Certificate

```nginx
server {
    listen 443 ssl;
    server_name mysite.com www.mysite.com;

    ssl_certificate     /etc/nginx/ssl/fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/privkey.pem;

    ssl_protocols       TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

> 🧠 If you’re using Let’s Encrypt, your cert paths are usually under /etc/letsencrypt/live/yourdomain.com/.

---

## ⚙️ Step 3: Optional – Auto-redirect WWW to non-WWW

```nginx
server {
    listen 80;
    server_name www.mysite.com;
    return 301 https://mysite.com$request_uri;
}
```

Same can be done the other way around if you prefer www. format.

---

## 🛠️ Real-World Tips

| Use Case                | Tip                                                  |
| ----------------------- | ---------------------------------------------------- |
| Let’s Encrypt + Certbot | Use `certbot --nginx` for automatic config           |
| HTTPS for custom domain | Get certs from a trusted CA or via Cloudflare        |
| Wildcard subdomains     | Use `server_name *.mysite.com;`                      |
| Force HTTPS globally    | Always include an HTTP block that redirects to HTTPS |

--- 

## 🔒 Optional: Improve SSL Security

```nginx
ssl_prefer_server_ciphers on;
ssl_session_timeout 1d;
ssl_session_cache shared:SSL:10m;
ssl_session_tickets off;
```

> These options boost security and compatibility for modern browsers.

---

## ✅ Summary

- Use listen 80 to catch HTTP, and redirect to https
- Use listen 443 ssl with valid certificate and key files
- Configure ssl_protocols, ssl_ciphers, and enable HSTS (optional)
- Use certbot to make life easier when using Let’s Encrypt

---
