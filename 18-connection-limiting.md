# 1️⃣8️⃣ How to protect against slowloris and connection-based attacks?

---

## 🧩 Problem Description

You want to:

- Protect your server from attacks like Slowloris, which keep connections open indefinitely to exhaust server resources.
- Limit the number of concurrent connections each client can make to your server.
- Ensure that your server remains responsive even under heavy traffic or malicious attacks.

✅ Connection limiting helps defend against attacks that attempt to overwhelm your server by consuming all available connections, such as Slowloris or DDoS-style connection floods.

---

## 📚 Core Nginx Concepts

- `limit_conn_zone` – defines the shared memory zone and connection rate limit
- `limit_conn` – applies the connection limit to specific locations or server blocks
- `$binary_remote_addr` – represents the client’s IP address in binary format, used for more efficient connection tracking
- `client_body_timeout` and `client_header_timeout` – control how long to wait for the client to send request bodies or headers

---

## ⚙️ Step-by-Step: Limiting Connections by IP

### 1. **Define a Shared Memory Zone for Connections**

In the `http` block of your Nginx configuration (usually in `nginx.conf`):

```nginx
http {
    limit_conn_zone $binary_remote_addr zone=conn_limit_per_ip:10m;

    include /etc/nginx/conf.d/*.conf;
}
```

💡 Explanation

- conn_limit_per_ip is the name of the shared memory zone
- 10m allocates 10MB of memory for connection tracking, enough for ~160,000 IP addresses
- $binary_remote_addr is used to track connections based on the client's IP

### 2. Apply Connection Limiting in the Server or Location Block

In the server or location block where you want to limit connections:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    # Limit the number of simultaneous connections per IP to 10
    limit_conn conn_limit_per_ip 10;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

💡 Explanation

- limit_conn conn_limit_per_ip 10; – This limits each IP address to 10 simultaneous connections. You can adjust this value depending on your traffic requirements.

### 3. Adjust Timeout Settings for Better Defense

To prevent attacks like Slowloris, adjust the connection timeout settings to make sure the server doesn't keep connections open indefinitely:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    # Connection timeout settings
    client_body_timeout 10s;
    client_header_timeout 10s;
    send_timeout 10s;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

💡 Explanation

- client_body_timeout 10s; – The server will wait for 10 seconds for the client to send the request body.
- client_header_timeout 10s; – The server will wait for 10 seconds for the client to send request headers.
- send_timeout 10s; – Controls how long the server will wait to send data to the client.

These settings help limit the impact of Slowloris attacks by reducing the time allowed for a client to keep a connection open without sending data.

---

## 🧪 Test It!

1. Test Connection Limits:

Use ab (Apache Bench) to test the connection limits:

```bash
ab -n 1000 -c 20 https://yourdomain.com/
```

Once the connection limit is reached, you should see 503 Service Unavailable responses, indicating that the server is limiting connections.

2. Test Slowloris Protection:

You can simulate a Slowloris attack using the Slowloris tool. After running the attack, you should see that your server starts rejecting or dropping connections due to the timeouts and connection limits.

---

## ⚠️ Common Issues and Considerations

| Issue                            | Solution/Tip                                                                                                   |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Over-restricting connections** | Ensure you’re not setting the connection limit too low, which could block legitimate users.                    |
| **Timeout causing disruptions**  | Carefully balance timeout settings to prevent disconnecting genuine clients while defending against Slowloris. |
| **False positives**              | Test your limits under real traffic conditions before deploying them to ensure they don't block normal users.  |

---

## ✅ Summary

- Limit concurrent connections: Use limit_conn to restrict the number of simultaneous connections per IP address.
- Defend against Slowloris: Use timeouts (client_body_timeout, client_header_timeout, etc.) to prevent attacks that keep connections open.
- Test: Ensure the connection limits and timeouts work as expected under load and simulated attacks.

---
