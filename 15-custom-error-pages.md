# 1️⃣5️⃣ How to configure custom error pages for better user experience?

---

## 🧩 Problem Description

You want to:

- Display a friendly, branded message when users encounter errors (404, 500, etc.)
- Avoid showing default, unstyled Nginx error pages
- Maintain UX consistency even during failure

✅ Custom error pages help you control the user experience, maintain trust, and provide helpful guidance when something goes wrong.

---

## 📚 Core Nginx Concepts

- `error_page` – defines a page to show when a specific HTTP error occurs
- `location` – maps the path of the custom error page
- `internal` – ensures the error page path can’t be accessed directly

---

## ⚙️ Step-by-Step: Creating Custom Error Pages

### 1. **Create Your Custom Error Pages**

First, design and place your custom HTML error files somewhere Nginx can access them, e.g.:

```bash
/var/www/html/errors/404.html
/var/www/html/errors/500.html
```

Example content of 404.html:

```html
<!DOCTYPE html>
<html lang="en">
<head><meta charset="UTF-8"><title>404 Not Found</title></head>
<body>
  <h1>Oops! Page not found.</h1>
  <p>The page you are looking for doesn’t exist.</p>
  <a href="/">Return to homepage</a>
</body>
</html>
```

### 2. Update Your Nginx Configuration

In your server block, define error pages and associate them with custom files:

```nginx
server {
    listen 443 ssl http2;
    server_name yourdomain.com;

    root /var/www/html;

    # Custom error page definitions
    error_page 404 /errors/404.html;
    error_page 500 502 503 504 /errors/500.html;

    location = /errors/404.html {
        root /var/www/html;
        internal;
    }

    location = /errors/500.html {
        root /var/www/html;
        internal;
    }

    location / {
        try_files $uri $uri/ =404;
    }
}
```

💡 Explanation

- error_page 404 /errors/404.html; – Maps a 404 error to your custom file
- internal; – Prevents users from accessing /errors/404.html directly
- root /var/www/html; – Make sure this matches where your files are located

---

## 🧪 Test It!

Test 404 Error:

- Visit a non-existent page: https://yourdomain.com/nonexistent-page
- You should see your custom 404 page

Test 500 Error:

- Temporarily simulate a 500 error by pointing to a broken backend or misconfigured script

Example:

```nginx
location /error-test {
    return 500;
}
```

Visit https://yourdomain.com/error-test and see if the 500 page loads.

---

## ⚠️ Common Issues

| Issue                               | Solution                                                     |
| ----------------------------------- | ------------------------------------------------------------ |
| Custom page doesn’t display         | Ensure the file path is correct and accessible               |
| File downloads instead of rendering | Set correct `Content-Type` in your HTML or Nginx config      |
| Custom pages not styled             | Use inline CSS or host CSS on the same domain via HTTPS      |
| Infinite error loops                | Do not redirect to dynamic pages for errors (keep it static) |

---

## ✅ Summary

- Use error_page to define custom error responses in Nginx
- Create static HTML pages for common errors like 404 and 500
- Use internal to prevent direct access to error page URLs
- Custom pages improve UX, reduce bounce rate, and build user trust

---
