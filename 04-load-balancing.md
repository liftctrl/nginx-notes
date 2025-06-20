# 4️⃣ How to load balance traffic across multiple backend servers?

---

## 🧩 Problem Description

You’ve deployed multiple backend instances for performance or reliability. Now you want:

- Incoming requests to be distributed across these servers
- Failover if one backend crashes
- Control over load distribution (round robin, IP hash, etc.)

📌 Nginx supports load balancing natively — no extra tools required.

---

## 📚 Core Nginx Concepts

- `upstream` block – defines a group of backend servers
- `proxy_pass` – sends traffic to the upstream group
- Load balancing algorithms: `round-robin` (default), `least_conn`, `ip_hash`

---

## ⚙️ Basic Load Balancing with `upstream`

```nginx
upstream backend {
    server 127.0.0.1:3000;
    server 127.0.0.1:3001;
    server 127.0.0.1:3002;
}

server {
    listen 80;
    server_name mysite.com;

    location /api/ {
        proxy_pass http://backend;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

💡 Explanation:

- Requests to /api/ are distributed across 3 backend ports
- The backend group is defined using the upstream block

---

## ⚙️ Load Balancing Algorithms 

### 1. Default: Round Robin

Distributes requests evenly across servers.

```nginx
upstream backend {
    server 10.0.0.1;
    server 10.0.0.2;
}
```

### 2. Least Connections (least_conn)

Sends traffic to the server with the fewest active connections.

```nginx
upstream backend {
    least_conn;
    server 10.0.0.1;
    server 10.0.0.2;
}
```

### 3. IP Hash (Sticky sessions)

Same IP always goes to the same server.

```nginx
upstream backend {
    ip_hash;
    server 10.0.0.1;
    server 10.0.0.2;
}
```

> ⚠️ Don't combine ip_hash with backup servers or unequal weights.

---

## ⚙️ Add Failover / Backup Server

```nginx
upstream backend {
    server 10.0.0.1;
    server 10.0.0.2;
    server 10.0.0.3 backup;
}
```

- backup server is only used if the others are down

---

## ⚠️ Optional: Configure Health Checks (commercial or 3rd party)

Open-source Nginx lacks native health checks unless you use:

- Nginx Plus (commercial)
- nginx_upstream_check_module (requires custom build)
- External health check scripts + fail_timeout hack:

```nginx
upstream backend {
    server 10.0.0.1 max_fails=3 fail_timeout=10s;
    server 10.0.0.2;
}
```

---

## 🛠️ Real-World Scenarios

| Use Case                   | Recommended Config              |
| -------------------------- | ------------------------------- |
| Scale Node.js backend      | Use `upstream` with round robin |
| Ensure minimal queueing    | Use `least_conn`                |
| Session stickiness (login) | Use `ip_hash`                   |
| One server = standby       | Use `backup`                    |

---

## 🧪 Test It!

Use curl or browser to hit the endpoint repeatedly:

```bash
curl http://mysite.com/api/test
```

Then inspect logs or backend output to confirm distribution.

---

## ✅ Summary

- Use upstream to define a backend group
- Choose a load balancing strategy: round-robin, least_conn, or ip_hash
- Use backup or fail_timeout for basic failover
- For health checks, consider external tools or Nginx Plus

---
