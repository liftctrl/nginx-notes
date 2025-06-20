# # 1️⃣ How to access backend services via domain name? (Reverse Proxy with `proxy_pass`)

---

## 🧩 Problem Description

You may encounter the following real-world issues:

- Your frontend can’t connect to the backend API via a domain
- CORS errors appear when making API requests
- In local dev: frontend runs on `localhost:5173`, backend on `localhost:3000`
- In production: exposing multiple backend ports isn’t safe or maintainable

📌 All of these can be solved using **Nginx reverse proxy**.

---

## 📚 Core Nginx Feature: `proxy_pass` (Reverse Proxy)

Nginx acts as a **reverse proxy**, which means:

- It receives client requests at a public-facing domain
- It forwards those requests internally to another server (e.g. Node.js, Flask)
- It sends the backend’s response back to the client

This approach hides internal services and provides a clean entry point.

---

## ⚙️ Minimal Configuration Example

```nginx
server {
    listen 80;
    server_name mysite.com;

    location /api/ {
        proxy_pass http://127.0.0.1:3000/;
    }
}
```

---

## 🔍 How this works:

- A client calls: http://mysite.com/api/users
- Nginx strips /api/ and forwards it as: http://127.0.0.1:3000/users

> 🧠 If you don’t want the /api/ to be stripped, remove the trailing / in proxy_pass.

---

## 🧠 How proxy_pass URL Rewriting Works

### Case A: With trailing slash

```nginx
location /api/ {
    proxy_pass http://127.0.0.1:3000/;
}
```

- Request: /api/user
- Forwarded: http://127.0.0.1:3000/user
- /api/ is stripped

### Case B: Without trailing slash

```nginx
location /api/ {
    proxy_pass http://127.0.0.1:3000;
}
```

- Request: /api/user
- Forwarded: http://127.0.0.1:3000/api/user
- Full original path is preserved

---

## 💡 Best Practice: Preserve Client Info

Always include these headers to pass real client IP and domain info:

```nginx
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```

### Full Recommended Block:

```nginx
location /api/ {
    proxy_pass http://127.0.0.1:3000/;

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
}
```

---

## 🛠️ Real-World Use Cases

| Use Case                    | How Nginx Helps                       |
| --------------------------- | ------------------------------------- |
| Frontend-backend separation | Proxy `/api` to backend services      |
| Local development           | Route frontend requests to backend    |
| Microservice gateway        | One entry point for many backend apps |
| Security enhancement        | Hide actual backend IP/port           |

---

## ✅ Summary

proxy_pass is one of the most powerful and essential Nginx directives. It allows you to cleanly route traffic from a public domain to internal services, helping you build modern web apps and secure systems.

---
