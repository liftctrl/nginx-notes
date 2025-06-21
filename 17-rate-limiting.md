# 1️⃣7️⃣ How to configure rate limiting to prevent abuse?

---

## 🧩 Problem Description

You want to:

- Protect your site from abusive clients, bots, or brute-force attacks
- Limit the number of requests from a single IP address
- Ensure fair usage of your API or resources

✅ Rate limiting helps prevent abuse and DoS-style attacks by throttling the number of requests a user can make within a given time window.

---

## 📚 Core Nginx Concepts

- `limit_req_zone` – defines the shared memory zone and request rate
- `limit_req` – applies rate limiting to a specific location or server block
- `$binary_remote_addr` – represents the client’s IP address in a binary format (more efficient)

---

## ⚙️ Step-by-Step: Basic Rate Limiting by IP

### 1. **Define a Shared Memory Zone**

In the `http` block of your Nginx configuration (usually in `nginx.conf`):

```nginx
http {
    limit_req_zone $binary_remote_addr zone=perip:10m rate=10r/s;

    include /etc/nginx/conf.d/*.conf;
}
```

💡 Explanation

- perip is the name of the zone
- 10m allocates 10MB of memory (enough for ~160,000 IPs)
- rate=10r/s allows 10 requests per second per IP

### 2. Apply Rate Limiting in a Location

Inside a server block or a location, apply the rule:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    location /api/ {
        limit_req zone=perip burst=20 nodelay;

        proxy_pass http://backend;
    }
}
```

💡 Explanation

- burst=20 – allows a burst of 20 requests above the rate limit
- nodelay – processes burst requests without delay (remove nodelay to space them out)

---

## 🧪 Test It!

Use a tool like ab (Apache Bench), wrk, or curl in a loop to test:

```bash
ab -n 100 -c 10 https://yourdomain.com/api/
```

Or:

```bash
for i in {1..100}; do curl -s -o /dev/null -w "%{http_code}\n" https://yourdomain.com/api/; done
```

You should start seeing 503 responses after the threshold is reached.

---

## ⚠️ Custom Error Response

By default, Nginx returns 503 Service Temporarily Unavailable when rate-limited. You can customize this:

```nginx
error_page 503 /rate-limit-error.html;

location = /rate-limit-error.html {
    root /var/www/html;
    internal;
}
```

Place your custom HTML at /var/www/html/rate-limit-error.html.

---

## 🛠 Advanced Usage

### 🔐 Rate Limiting Only on Login or Sensitive Routes

Apply to specific routes like login:

```nginx
location /login {
    limit_req zone=perip burst=5 nodelay;
    proxy_pass http://backend;
}
```

### 📱 Rate Limiting Based on Custom Keys (e.g., API Keys)

You can limit based on a header (e.g., API key) using a variable:

```nginx
map $http_x_api_key $api_limit_key {
    default $binary_remote_addr;
    "key-123" key123;
    "key-456" key456;
}

limit_req_zone $api_limit_key zone=api_limit:10m rate=5r/s;

location /api/ {
    limit_req zone=api_limit burst=10;
}
```

---

## ✅ Summary

- Use limit_req_zone to define rate limits by IP or custom key
- Use limit_req to apply them to specific routes or APIs
- Customize burst behavior and fallback responses
- Useful for login forms, APIs, contact forms, or any endpoint vulnerable to brute-force or scraping

---
