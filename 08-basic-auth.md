# 8️⃣ How to protect a site or route with basic authentication?

---

## 🧩 Problem Description

You need to:

- Restrict access to a staging site or admin panel
- Add password protection without a full login system
- Prevent unauthorized users from accessing sensitive routes

✅ Nginx supports basic HTTP authentication using `.htpasswd` files.

---

## 📚 Core Nginx Concepts

- `auth_basic` – enables HTTP Basic Authentication
- `auth_basic_user_file` – path to a password file in `.htpasswd` format
- `.htpasswd` – file containing usernames and encrypted passwords

---

## ⚙️ Step-by-Step: Protect a Route with Basic Auth

### 1. 🔐 Generate a Password File

Use `htpasswd` (from Apache tools):

```bash
sudo apt install apache2-utils     # Debian/Ubuntu
htpasswd -c /etc/nginx/.htpasswd  yourusername
```

You'll be prompted to enter a password.

> Use -c only the first time to create the file (don't use it again when adding more users).

### 2. 🔧 Update Your Nginx Config

```nginx
location /admin/ {
    auth_basic "Restricted Area";
    auth_basic_user_file /etc/nginx/.htpasswd;

    proxy_pass http://backend/admin/;
}
```

💡 Explanation

- "Restricted Area" will show up in the login dialog
- Only users listed in .htpasswd can access /admin/

---

## ⚠️ Security Tips

| Concern          | Recommendation                           |
| ---------------- | ---------------------------------------- |
| Password storage | Never store passwords in plain text      |
| HTTPS required   | Use HTTPS to avoid credentials over HTTP |
| Hide .ht\* files | Deny access to `.htpasswd` in your root: |

```nginx
location ~ /\.ht {
    deny all;
}
```

---

## 🛠️ Real-World Scenarios

| Use Case                  | Example Config                          |
| ------------------------- | --------------------------------------- |
| Protect staging site      | Use `auth_basic` on entire server block |
| Protect API docs          | Restrict `/docs/` location              |
| Require login for backups | Protect `/downloads/backup.zip`         |

---

## 🧪 Test It!

Visit the protected route:

```bash
curl -u yourusername http://yoursite.com/admin/
```

Or in browser → you’ll see a popup asking for login credentials.

Invalid credentials will return:

```http
401 Unauthorized
```

---

## ✅ Summary

- Use auth_basic to enable password protection
- Use .htpasswd format to store encrypted credentials
- Always use HTTPS with Basic Auth to protect credentials
- Great for staging sites, admin panels, and internal tools

---
