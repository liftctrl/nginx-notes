# 2️⃣ How to serve static files? (`root` vs `alias` in Nginx)

---

## 🧩 Problem Description

You want to:

- Serve an HTML frontend (e.g. React, Vue, or plain HTML)
- Serve static assets like images, CSS, JS, or downloadable files
- Build a simple static website using Nginx

📌 This is where Nginx shines — it’s extremely fast at serving static content.

---

## 📚 Core Nginx Directives: `root` vs `alias`

Nginx provides two ways to define where static files are located:

| Directive | Purpose                              |
|----------:|--------------------------------------|
| `root`    | Appends the request URI to the path  |
| `alias`   | Replaces the matched location with the path |

---

## ⚙️ Example 1: Using `root`

```nginx
server {
    listen 80;
    server_name mysite.com;

    location / {
        root /var/www/html;
        index index.html;
    }
}
```

💡 Explanation:

- Request: /about.html
- File served: /var/www/html/about.html

> root takes the entire request URI (after the location) and appends it to the given path.

---

## ⚙️ Example 2: Using alias 

```nginx
location /static/ {
    alias /var/www/assets/;
}
```

💡 Explanation:

- Request: /static/img/logo.png
- File served: /var/www/assets/img/logo.png

> alias replaces the location prefix (/static/) with the given path.

---

## 🆚 root vs alias Comparison Table

| Request           | Location   | Directive      | File Served            |
| ----------------- | ---------- | -------------- | ---------------------- |
| `/images/pic.jpg` | `/images/` | `root /data/`  | `/data/images/pic.jpg` |
| `/images/pic.jpg` | `/images/` | `alias /data/` | `/data/pic.jpg`        |

> 🚨 With alias, you must match the location path exactly as it’s used in requests, or you’ll get a 404.

---

## 💡 Common Gotchas

1. When using alias, do not forget the trailing /

✅ alias /path/to/files/;
❌ alias /path/to/files; may cause path join issues

2. Use try_files for SPA (Single Page Application) routing

```nginx
location / {
    root /var/www/app;
    try_files $uri $uri/ /index.html;
}
```

This ensures routes like /about or /dashboard/stats still serve index.html (for React Router, Vue Router, etc.).

---

## 🛠️ Real-World Use Cases

| Scenario                        | Nginx Config                        |
| ------------------------------- | ----------------------------------- |
| Serve static site from a folder | `root` or `alias` + `index`         |
| Serve images from `/static/img` | Use `alias` to map to assets folder |
| Host frontend app (React/Vue)   | Use `try_files` fallback            |
| Custom file downloads           | Serve from hidden folder via alias  |

---

## ✅ Summary

- Use root when your request path matches the folder structure
- Use alias when you want to rewrite the URI path internally
- Always use trailing slashes in alias
- For SPAs, use try_files $uri $uri/ /index.html;

---
