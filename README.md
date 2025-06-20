# 🚀 Nginx Notes – Problem-Driven Learning Manual

> Learn Nginx by solving real-world problems.  
> Each note starts with a situation and teaches you how to fix it using Nginx.

---

## 📘 Topics

| #  | Topic                                                   | File                         |
|----|----------------------------------------------------------|------------------------------|
| 1  | How to access backend via domain? (Reverse Proxy)       | [01-reverse-proxy.md](./01-reverse-proxy.md) |
| 2  | How to serve static assets? (`root` vs `alias`)         | [02-static-files.md](./02-static-files.md) |
| 3  | How to enable HTTPS and redirect HTTP?                  | [03-https-redirect.md](./03-https-redirect.md) |
| 4  | How to load balance across multiple servers?            | [04-load-balancing.md](./04-load-balancing.md) |
| 5  | How to set browser cache headers?                       | [05-browser-cache.md](./05-browser-cache.md) |
| 6  | How to add health check and fallback?                   | [06-health-check.md](./06-health-check.md) |
| 7  | How to allow or deny certain IPs?                       | [07-ip-whitelist.md](./07-ip-whitelist.md) |
| 8  | How to limit request rate for APIs?                     | [08-limit-req.md](./08-limit-req.md) |
| 9  | How to fix CORS issues in frontend-backend calls?       | [09-cors.md](./09-cors.md) |
| 10 | How to customize 404 and 500 error pages?               | [10-error-pages.md](./10-error-pages.md) |
| 11 | How does `location` matching priority work?             | [11-location-matching.md](./11-location-matching.md) |
| 12 | How to quickly mock an API with Nginx?                  | [12-mock-api.md](./12-mock-api.md) |
| 13 | How to enable Gzip compression?                         | [13-gzip-compression.md](./13-gzip-compression.md) |
| 14 | How to prevent image hotlinking?                        | [14-hotlink-protection.md](./14-hotlink-protection.md) |
| 15 | How to deploy Nginx with Docker easily?                 | [15-docker-deploy.md](./15-docker-deploy.md) |

---

## 📂 Folder Structure

```bash
nginx-notes/
├── README.md                   # 项目总览与目录说明
├── 01-reverse-proxy.md         # Q1: 反向代理 proxy_pass
├── 02-static-files.md          # Q2: 静态资源 root vs alias
├── 03-https-redirect.md        # Q3: 配置 HTTPS 和强制跳转
├── 04-load-balancing.md        # Q4: upstream 负载均衡
├── 05-browser-cache.md         # Q5: 浏览器缓存配置
├── 06-health-check.md          # Q6: 健康检查与服务容错
├── 07-ip-whitelist.md          # Q7: IP 白名单/黑名单
├── 08-limit-req.md             # Q8: 请求频率限制 limit_req
├── 09-cors.md                  # Q9: CORS 跨域处理
├── 10-error-pages.md           # Q10: 自定义错误页面
├── 11-location-matching.md     # Q11: location 匹配规则详解
├── 12-mock-api.md              # Q12: 使用 Nginx 创建 Mock API
├── 13-gzip-compression.md      # Q13: Gzip 加速压缩配置
├── 14-hotlink-protection.md    # Q14: 防盗链配置
└── 15-docker-deploy.md         # Q15: 使用 Docker 快速部署 Nginx
```

---

## 🧠 Why Problem-Driven?

Each note follows a simple yet powerful format:

- What problem are we solving?
- What Nginx feature solves it?
- What does the config look like?
- Why does it work that way?
- Where is it useful in real life?

---

## 📌 License

MIT — use it, fork it, learn it, share it.
