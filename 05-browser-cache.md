# 5️⃣ How to configure browser caching for static assets?

---

## 🧩 Problem Description

You deployed your frontend, and you notice:

- Clients always download the same JS/CSS/images, even if they didn’t change
- Site loads slowly due to repeated static file requests
- You want to leverage browser cache to reduce server load and improve speed

✅ You can solve this with **cache-control headers** via Nginx.

---

## 📚 Core Nginx Concepts

- `expires` – sets how long a file should be cached in the browser
- `add_header Cache-Control` – adds fine-grained cache rules
- `types` – match file extensions to set different rules

---

## ⚙️ Basic Static Cache Config

```nginx
location ~* \.(js|css|png|jpg|jpeg|gif|svg|ico|woff2?)$ {
    expires 30d;
    add_header Cache-Control "public";
}
```

💡 Explanation

- Matches static file types using regex
- expires 30d tells browser to cache for 30 days
- Cache-Control: public allows caching by CDN and browser

---

## ⚙️ Cache Static HTML Differently

You may want shorter caching for .html files:

```nginx
location ~* \.html$ {
    expires 5m;
    add_header Cache-Control "no-cache, must-revalidate";
}
```

> no-cache ensures the browser checks with the server before using cached HTML — useful for SPAs.

---

## ⚙️ Full Static Caching Block Example

```nginx
location ~* \.(js|css|png|jpg|jpeg|gif|svg|ico|woff2?)$ {
    expires 30d;
    add_header Cache-Control "public, max-age=2592000, immutable";
}
```

- immutable: browser will never revalidate unless filename changes
- Use this with versioned file names like main.8fjs3k.js

---

## ⚠️ Avoid Caching Pitfalls

| File Type | Strategy                            | Why                                 |
| --------- | ----------------------------------- | ----------------------------------- |
| JS/CSS    | Cache long-term, use hash filenames | Reduce load and support versioning  |
| HTML      | No-cache or short TTL               | Ensure content stays up to date     |
| Images    | Cache long-term                     | Rarely change, heavy to re-download |

---

## 🛠️ Real-World Scenario: React or Vue Build

If your build tool outputs hashed filenames like:

```bash
/assets/js/main.f8a1c3e2.js
/assets/css/style.9c3d.css
```

✅ Use aggressive caching:

```nginx
location /assets/ {
    expires 1y;
    add_header Cache-Control "public, max-age=31536000, immutable";
}
```

---

## 🧪 Test It!

Use browser DevTools → Network tab → Refresh and inspect headers:

```http
Cache-Control: public, max-age=2592000, immutable
Expires: Wed, 24 Jul 2025 14:00:00 GMT
```

---

## ✅ Summary

- Use expires + Cache-Control to control browser caching
- Aggressive caching for hashed static files
- Short/no caching for dynamic or HTML files
- Improves speed and reduces bandwidth usage

---
