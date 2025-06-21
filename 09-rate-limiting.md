# 9️⃣ How to limit request rate to prevent abuse or DDoS?

---

## 🧩 Problem Description

You notice:

- Bots hammering your site with too many requests
- Users refreshing APIs hundreds of times per minute
- You want to protect your site from abuse without breaking legitimate usage

✅ Nginx offers built-in directives for basic rate limiting using the `limit_req` module.

---

## 📚 Core Nginx Concepts

- `limit_req_zone` – defines a shared memory zone to track request rates
- `limit_req` – applies a limit to specific locations
- `$binary_remote_addr` – uses IP address as a unique key
- `burst` – allows temporary spikes
- `nodelay` – controls how burst requests are handled

---

## ⚙️ Step-by-Step: Basic Rate Limiting per IP

### 1. 🧠 Define a Rate Limit Zone (in `http {}` block)

```nginx
limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
```

- zone=one:10m – name one, 10MB memory (~160k IPs)
- rate=1r/s – 1 request per second per IP

---

## 2. 🧱 Apply Limit to a Location

```nginx
location /api/ {
    limit_req zone=one burst=5 nodelay;
    proxy_pass http://backend;
}
```

💡 Explanation

- Allows 1 req/sec
- Allows short bursts up to 5 requests
- nodelay causes burst requests to be rejected immediately if exceeded

---

## ⚠️ Response Behavior

| Client Behavior            | Nginx Response                |
| -------------------------- | ----------------------------- |
| Over rate but within burst | 200 OK (if `nodelay` not set) |
| Over burst                 | `503 Service Unavailable`     |

Use this header to identify rate-limited requests:

```nginx
add_header X-RateLimit-Remaining $limit_req_status;
```

---

## 🧪 Test It!

```bash
# Send 10 requests in 1 second
for i in {1..10}; do curl -s -o /dev/null -w "%{http_code}\n" http://yoursite.com/api/; done
```

Expected output: First few 200s, followed by 503s.

---

## 🛠️ Optional: Limit by URL or User-Agent

Limit access to login page:

```nginx
location = /login {
    limit_req zone=one burst=3;
}
```

Limit bots via User-Agent:

```nginx
map $http_user_agent $bot_limit {
    default         "";
    ~*bot           $binary_remote_addr;
}

limit_req_zone $bot_limit zone=bots:10m rate=1r/m;

location / {
    limit_req zone=bots;
}
```

---

## ✅ Summary

- Use limit_req_zone to define IP-based rate limits
- Apply per location with limit_req
- Use burst to handle short spikes
- Helps mitigate scraping, abuse, and light DDoS attempts

---
