# 🔟 How to restrict access by IP or network?

---

## 🧩 Problem Description

You want to:

- Make a site or admin panel accessible only to specific IPs
- Prevent unauthorized public access to internal tools
- Secure a staging site from search engines or the public

✅ Nginx can easily restrict access using the `allow` and `deny` directives.

---

## 📚 Core Nginx Directives

- `allow` – explicitly permits an IP or CIDR block
- `deny` – denies access to everyone else
- Order matters: allow/deny rules are checked in order
- Can be used inside `server`, `location`, or `limit_except` blocks

---

## ⚙️ Example: Restrict Access by IP

```nginx
location /admin/ {
    allow 192.168.1.100;
    allow 203.0.113.0/24;
    deny all;

    proxy_pass http://backend/admin/;
}
```

💡 Explanation

- Only requests from 192.168.1.100 and 203.0.113.0/24 are allowed
- Everyone else is denied with a 403 Forbidden response

---

## ⚙️ Use Case: Protect Entire Site

```nginx
server {
    listen 80;
    server_name staging.example.com;

    allow 203.0.113.10;
    deny all;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

---

## ⚙️ Alternative: Block Specific IPs

```nginx
location / {
    deny 1.2.3.4;
    deny 5.6.7.0/24;
    allow all;

    proxy_pass http://backend;
}
```

Use this to ban known malicious IPs.

---

## ⚠️ Notes and Gotchas

| Issue                  | Solution or Tip                                         |
| ---------------------- | ------------------------------------------------------- |
| IPv6 support           | Add `allow` and `deny` for IPv6 addresses if needed     |
| Proxy headers involved | Use `$http_x_forwarded_for` only if you trust the proxy |
| No default `allow all` | Always use `deny all` as the last rule                  |

---

## 🧪 Test It!

Try from:

- Allowed IP: ✅ Should return 200 OK
- Blocked IP: ❌ Should return 403 Forbidden

Use curl with spoofed IP headers to simulate:

```bash
curl -H "X-Forwarded-For: 203.0.113.10" http://yoursite.com/admin/
```

Only works if you explicitly trust proxy headers (not safe by default).

---

## ✅ Summary

- Use allow and deny to control access by IP
- Useful for staging, admin, monitoring, backups, etc.
- CIDR blocks can cover IP ranges
- Always add deny all as a fallback

---
