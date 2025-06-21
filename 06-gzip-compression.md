# 6️⃣ How to configure gzip compression to reduce file sizes?

---

## 🧩 Problem Description

You want to:

- Speed up page load times
- Reduce bandwidth usage
- Compress large text-based assets like HTML, CSS, JS, JSON, etc.

📌 Nginx supports gzip compression natively — it’s easy to enable and very effective.

---

## 📚 Core Nginx Directives

- `gzip` – master switch
- `gzip_types` – specify which MIME types to compress
- `gzip_min_length` – skip small files
- `gzip_vary` – inform proxies/browsers that both compressed and uncompressed versions exist

---

## ⚙️ Basic Gzip Setup

```nginx
gzip on;
gzip_disable "msie6";

gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_min_length 1024;

gzip_types
    text/plain
    text/css
    application/json
    application/javascript
    application/x-javascript
    text/xml
    application/xml
    application/xml+rss
    text/javascript;
```

💡 Explanation

| Directive            | Purpose                                           |
| -------------------- | ------------------------------------------------- |
| `gzip on;`           | Enables gzip globally                             |
| `gzip_disable`       | Prevents gzip for very old IE browsers (optional) |
| `gzip_vary on;`      | Adds `Vary: Accept-Encoding` header               |
| `gzip_proxied any;`  | Allows compression for proxied requests           |
| `gzip_comp_level 6;` | Sets compression level (1–9, higher = more CPU)   |
| `gzip_min_length`    | Don't compress very small files (<1KB)            |
| `gzip_types`         | MIME types eligible for gzip                      |

---

## 🛠️ Real-World Example

You can place this in your main nginx.conf under the http {} block:

```nginx
http {
    include       mime.types;
    default_type  application/octet-stream;

    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_min_length 1024;
    gzip_types
        text/plain
        text/css
        application/json
        application/javascript
        text/xml
        application/xml
        application/xml+rss
        text/javascript;

    ...
}
```

⚠️ What NOT to gzip

- Already compressed files (e.g. images, videos, PDFs)
- Binary downloads (e.g. .zip, .exe)
- Small files (<1KB) — compression can increase size due to headers

> 🔍 Nginx won't gzip unless the file's MIME type is in gzip_types.

---

## 🧪 Test It!

### Use curl:

```bash
curl -H "Accept-Encoding: gzip" -I https://yoursite.com/style.css
```

Look for:

```bash
Content-Encoding: gzip
```

### Use browser DevTools:

- Open Network tab
- Reload with cache disabled
- Look under "Headers" → check for Content-Encoding: gzip

---

## ✅ Summary

- Turn on gzip globally with gzip on;
- Target only text-based content with gzip_types
- Avoid compressing images and already-compressed files
- Use gzip_comp_level 4–6 for balanced performance

---
