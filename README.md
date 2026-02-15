# Connect .bd Domain to Cloudflare Pages (Using Worker Proxy)

This guide explains how to connect a `.bd` domain to Cloudflare Pages when the domain cannot be added directly in:

Workers & Pages → Custom Domains

If you see this error:

```
Please enter a valid domain
```

This solution will help you connect your domain using a Cloudflare Worker.

---

# 📌 Who Is This For?

This guide is for users who:

- Own a `.bd` domain
- Use Cloudflare Pages
- Cannot attach the domain directly in Pages
- Want a free and fully proxied solution
- Do not want to use external hosting

---

# ❗ The Problem

Sometimes Cloudflare Pages does not accept certain country TLDs like `.bd`.

Even if:

- The domain is active
- DNS is correctly configured
- Proxy (Orange Cloud) is enabled
- SSL is configured
- The domain works fine with Cloudflare Workers

Pages custom domain validation may still reject it.

---

# ✅ Working Architecture

Root Domain  
→ Redirect to WWW  
→ Cloudflare Worker  
→ Cloudflare Pages  
→ Static Site  

---

# 🌍 Example (Real Setup)

Example domain:

```
hossain.bd
```

Example Pages project:

```
hossainbd.pages.dev
```

You can replace these with your own domain and Pages project.

---

# 🚀 Step-by-Step Setup

---

## 1️⃣ Create a Cloudflare Worker

Go to:

Cloudflare Dashboard  
→ Workers & Pages  
→ Create  
→ Start with Hello World  

Replace the default code with:

```javascript
export default {
  async fetch(request) {
    const url = new URL(request.url)
    url.hostname = "your-project.pages.dev"
    return fetch(url, request)
  }
}
```

Replace:

```
your-project.pages.dev
```

With your own Pages project URL.

Click Deploy.

---

## 2️⃣ Add Worker Route

Go to:



Worker → Settings → Domains & Routes → Add Route  

<img width="395" height="916" alt="image" src="https://github.com/user-attachments/assets/7f9af47d-08fa-480d-85da-77dd3e8d74e7" />

Add:

```
*.yourdomain.bd/*
```

Example:

```
*.hossain.bd/*
```

Failure mode:

```
Fail closed (block)
```

Save.

---

## 3️⃣ Configure DNS

Go to:

Cloudflare → DNS  

Add:

Type: A  
Name: @  
Content: 192.0.2.1  
Proxy: ON  

Type: A  
Name: www  
Content: 192.0.2.1  
Proxy: ON  

Make sure the Proxy (Orange Cloud) is enabled.

---

## 4️⃣ Redirect Root to WWW

Go to:

Cloudflare → Rules → Page Rules → Create Rule  

<img width="863" height="807" alt="image" src="https://github.com/user-attachments/assets/c6cabf4b-72fd-4d1b-ac85-d8c52861440b" />

URL:

```
yourdomain.bd/*
```

Example:

```
hossain.bd/*
```

Setting:

Forwarding URL  
301 – Permanent Redirect  

Destination:

```
https://www.yourdomain.bd/$1
```

Example:

```
https://www.hossain.bd/$1
```

Save and Deploy.

---

## 5️⃣ SSL Configuration

Go to:

Cloudflare → SSL/TLS → Overview  

Set mode to:

```
Full
```

---

# 🎯 Final Result

- https://yourdomain.bd → Redirects to www
- https://www.yourdomain.bd → Loads via Worker
- Worker proxies to Pages
- Fully protected by Cloudflare
- Works on Free plan

---

# 🔍 Troubleshooting

If root domain shows NXDOMAIN:

- Confirm nameservers point to Cloudflare
- Confirm A record with Name @ exists
- Wait for DNS propagation

If site is slow:

- Purge cache
- Check for redirect loops
- Hard refresh browser

If Worker is not triggering:

- Confirm route matches `*.yourdomain.bd/*`
- Confirm Proxy is enabled

---

# 🎯 Why This Method Is Safe

- No external server required
- Fully proxied
- Cloudflare SSL supported
- Works with Free plan
- Easy to maintain

---

# 📜 License

MIT License
