# 1️⃣6️⃣ How to prevent clickjacking and XSS with security headers?

---

## 🧩 Problem Description

You want to:

- Protect your website from clickjacking and cross-site scripting (XSS) attacks
- Ensure that your site is secure against common web vulnerabilities by using HTTP headers
- Improve your site's security posture by implementing best practices in Nginx configuration

✅ Security headers are an easy and effective way to protect your website from common security threats. Proper use of these headers helps mitigate risks like XSS, clickjacking, and content injection attacks.

---

## 📚 Core Nginx Concepts

- `add_header` – used to add custom HTTP headers in Nginx responses
- `X-Frame-Options` – prevents your site from being embedded in iframes (clickjacking protection)
- `Content-Security-Policy (CSP)` – prevents malicious content injection and reduces XSS risks
- `X-XSS-Protection` – enables or disables the browser's built-in XSS filtering
- `Strict-Transport-Security (HSTS)` – forces HTTPS connections for improved security

---

## ⚙️ Step-by-Step: Adding Security Headers in Nginx

### 1. **Clickjacking Protection: Use X-Frame-Options**

Clickjacking happens when a malicious site embeds your site within an invisible iframe to trick users into interacting with your content.

To prevent this, use the `X-Frame-Options` header:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    # Prevent clickjacking
    add_header X-Frame-Options "SAMEORIGIN" always;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

💡 Explanation

- X-Frame-Options "SAMEORIGIN": Ensures that your site can only be embedded in an iframe on the same origin (i.e., your domain). You could also use DENY to block embedding completely.

### 2. XSS Protection: Use X-XSS-Protection

Modern browsers have built-in features to protect against some types of XSS attacks. The X-XSS-Protection header enables or disables this filtering.

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    # Enable XSS protection in browsers
    add_header X-XSS-Protection "1; mode=block" always;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

💡 Explanation

- X-XSS-Protection "1; mode=block": Activates the XSS filter in browsers and blocks the page from loading if an attack is detected. This is supported by most modern browsers.

### 3. Content Security Policy (CSP)

CSP is one of the most effective ways to prevent malicious scripts from being executed in the browser. It defines a set of rules for where resources (e.g., scripts, styles, images) can be loaded from.

Here’s an example of a basic CSP header:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    # Content Security Policy to protect against XSS
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';" always;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

💡 Explanation

- default-src 'self': Allows resources (scripts, images, etc.) to be loaded only from the same domain
- script-src 'self' 'unsafe-inline': Allows inline scripts to be executed (be careful with this setting)
- style-src 'self' 'unsafe-inline': Allows inline styles to be used (again, be cautious with this)

You can refine this further by specifying trusted third-party domains like Google Fonts, CDN providers, etc.

### 4. Strict Transport Security (HSTS)

If you haven't already configured HSTS (covered in previous sections), now is the time to enforce HTTPS for your entire domain. This prevents man-in-the-middle attacks and ensures all future traffic uses HTTPS.

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    # HSTS header to enforce HTTPS
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

---

## ⚠️ Common Issues and Considerations

| Issue                                     | Solution/Tip                                                                                                        |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **Too restrictive CSP**                   | Ensure trusted resources (e.g., CDN, external scripts) are allowed in your CSP policy                               |
| **XSS protection breaking functionality** | Avoid setting `X-XSS-Protection: 1; mode=block` if using third-party scripts that might trigger XSS filters         |
| **CSP causing resources to be blocked**   | Debug CSP issues using browser DevTools by checking blocked resources and adjusting `Content-Security-Policy` rules |

---

## 🧪 Test It!

1. Test Clickjacking Protection:

- Try embedding your site in an iframe from a different domain. You should see the page blocked.

Example test:

```html
<iframe src="https://yourdomain.com"></iframe>
```

If the X-Frame-Options header is set correctly, the page won’t load.

2. Test XSS Protection:

- Test if the browser blocks pages with potential XSS vulnerabilities by visiting a known vulnerable page (e.g., a page that includes unsanitized user input).

3. Test CSP:

- Use browser DevTools to check the “Console” tab for any CSP violations.
- You should see warnings for any resources not permitted by the CSP policy.

4. Test HSTS:

- Clear your browser cache and cookies, then attempt to visit your site over HTTP. It should automatically redirect to HTTPS.

---

## ✅ Summary

- Clickjacking: Use the X-Frame-Options header to prevent your site from being embedded in iframes.
- XSS Protection: Enable X-XSS-Protection to activate the browser's XSS filter.
- Content Security Policy (CSP): Prevent malicious content by defining where resources can be loaded from.
- HSTS: Enforce HTTPS for all future connections to your site, preventing downgrade attacks.

---
