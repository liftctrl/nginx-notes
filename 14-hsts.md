# 1️⃣4️⃣ How to enable HTTP Strict Transport Security (HSTS)?

---

## 🧩 Problem Description

You want to:

- Force browsers to always use HTTPS for your site
- Enhance security by preventing downgrade attacks (where HTTP is used instead of HTTPS)
- Ensure that HTTPS is used even on future visits, even if a user types `http://` manually

✅ HTTP Strict Transport Security (HSTS) is a security feature that helps to enforce HTTPS by telling browsers to only connect to your site over HTTPS.

---

## 📚 Core Nginx Concepts

- `Strict-Transport-Security` (HSTS) – a header that tells browsers to always connect over HTTPS
- `max-age` – specifies how long the browser should remember the HTTPS-only rule (in seconds)
- `includeSubDomains` – applies the HSTS policy to all subdomains
- `preload` – allows your site to be added to the HSTS preload list maintained by browsers (optional, but recommended for high security)

---

## ⚙️ Step-by-Step: Enabling HSTS

### 1. **Add the HSTS Header in Nginx**

To enable HSTS, add the following header in your Nginx HTTPS server block:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;

    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    # HSTS header for strict HTTPS enforcement
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

    location / {
        proxy_pass http://backend;
    }
}
```

💡 Explanation

- max-age=31536000 – Tells browsers to only use HTTPS for the next year (in seconds: 31536000 = 1 year)
- includeSubDomains – Ensures that the policy applies to all subdomains
- preload – Indicates that your site is eligible for inclusion in the HSTS preload list maintained by browsers

> Important: Once you add preload, your domain will be permanently forced to use HTTPS and cannot be reverted easily.

### 2. Test HSTS

Once you’ve added the header, reload your Nginx configuration:

```bash
sudo systemctl reload nginx
```

Then, you can test it in your browser and via command line.

Browser Test:

1. Open Chrome, Firefox, or another modern browser.
2. Open the DevTools (F12), and go to the "Network" tab.
3. Reload the page and inspect the response headers.
4. Look for Strict-Transport-Security in the response headers.

You should see something like this:

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

Command Line Test:

You can also check the header using curl:

```bash
curl -I https://yourdomain.com
```

You should see:

```http
HTTP/2 200
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
```

### 3. Preload Your Domain (Optional)

If you want your domain to be included in the HSTS preload list (which is supported by most browsers), follow these steps:

- Go to the HSTS Preload Submission Site
- Submit your domain (e.g., yourdomain.com) for inclusion
- This ensures that browsers will enforce HTTPS from the first visit, even before the first connection to your site

> Important: Once your domain is added to the HSTS preload list, you cannot remove it easily. Make sure you're certain you want this step.

---

## ⚠️ Considerations and Warnings

| Concern                               | Solution/Tip                                                                                                     |
| ------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Forgetting HTTP to HTTPS Redirect** | HSTS relies on HTTPS being available. Make sure you already have HTTP to HTTPS redirects configured.             |
| **Not testing with HSTS**             | Before applying `preload`, thoroughly test your site to ensure it works correctly over HTTPS.                    |
| **Site-wide HTTPS**                   | HSTS affects all subdomains and resources (e.g., images, scripts), so make sure everything is served over HTTPS. |
| **Temporary rollback**                | If you add `preload`, removing it will require waiting up to a year and removing from the HSTS preload list.     |

---

## 🧪 Test It!

1. Try to access your site using HTTP (e.g., http://yourdomain.com). It should redirect to HTTPS.
2. After visiting HTTPS once, try typing http:// again into the browser. It should automatically be forced to HTTPS due to the HSTS policy.
3. Verify using curl:

```bash
curl -I https://yourdomain.com
```

The Strict-Transport-Security header should appear, confirming HSTS is active.

---

## ✅ Summary

- HSTS forces browsers to connect only via HTTPS, improving security by preventing downgrade attacks
- The Strict-Transport-Security header is added to the Nginx server block
- max-age defines how long the HSTS policy lasts, includeSubDomains applies to all subdomains, and preload adds your site to the HSTS preload list
- Once preload is enabled, it can’t be undone easily, so proceed with caution

---
