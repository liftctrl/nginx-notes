# 1️⃣2️⃣ How to enable HTTP/2 for better performance?

---

## 🧩 Problem Description

You want to:

- Improve website performance
- Take advantage of HTTP/2 features like multiplexing and header compression
- Modernize your site’s protocol for better speed and user experience

✅ HTTP/2 improves web performance significantly by allowing multiple requests over a single TCP connection, reducing latency and enhancing page load times.

---

## 📚 Core Nginx Concepts

- `http2` – enables HTTP/2 support in Nginx
- `listen` – need to specify `ssl` to use HTTP/2 over HTTPS
- TLS 1.2 or higher required – HTTP/2 only works with encrypted connections (HTTPS)

---

## ⚙️ Step-by-Step: Enabling HTTP/2 in Nginx

### 1. **Ensure HTTPS is Enabled**

HTTP/2 only works over secure connections. Make sure you have SSL (via Let’s Encrypt or other) already set up.

If you need a refresher on SSL configuration, check the previous section on [SSL with Let’s Encrypt](./11-ssl-lets-encrypt.md).

---

### 2. **Modify Your Nginx Configuration**

To enable HTTP/2, update your SSL server block by adding the `http2` flag to the `listen` directive.

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

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

- listen 443 ssl http2; enables both HTTPS and HTTP/2
- Make sure your SSL settings are correct before enabling HTTP/2

### 3. Verify HTTP/2 Support

Once you have updated your configuration and reloaded Nginx (sudo systemctl reload nginx), test that HTTP/2 is enabled.

Browser Test:

- Open Chrome or Firefox Developer Tools (F12)
- Go to the "Network" tab
- Reload your site and check the "Protocol" column
- You should see h2 for HTTP/2 connections

Command Line Test:

Use curl to check the protocol version:

```bash
curl -I --http2 https://yourdomain.com
```

You should see something like this in the response headers:

```http
HTTP/2 200
```

---

## ⚠️ Things to Consider

| Concern                       | Solution or Tip                                                                      |
| ----------------------------- | ------------------------------------------------------------------------------------ |
| Mixed content                 | Ensure all resources (JS, CSS, images) are loaded over HTTPS                         |
| Browser support               | HTTP/2 is supported by all modern browsers (Chrome, Firefox, Safari)                 |
| HTTP/2 over HTTP/1.1 fallback | If the client doesn't support HTTP/2, Nginx will fall back to HTTP/1.1 automatically |
| Enabling on HTTP/1.1          | HTTP/2 only works on HTTPS; you can’t enable HTTP/2 on plain HTTP                    |

---

## 🧪 Test It!

Check HTTP/2 with Curl:

```bash
curl -I --http2 https://yourdomain.com
```

If successful, you’ll see:

```bash
HTTP/2 200 OK
```

Also, verify in your browser DevTools:

1. Open DevTools (F12)
2. Go to the "Network" tab
3. Reload the page
4. Check for the h2 protocol

---

## ✅ Summary

- HTTP/2 improves performance by allowing multiple requests over a single connection
- Only works over HTTPS — so SSL configuration is required
- Enable with the http2 flag in the listen directive for SSL-enabled server blocks
- Test using DevTools or curl to confirm

---
