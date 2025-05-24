
# 📦 Deployment Workflow Documentation – Kids Accessories E-Commerce

This document captures the full technical journey of setting up and deploying the `Frontend` (built with Vite) into the `Backend` (Express server) hosted on an AWS EC2 instance. It includes all attempted solutions, issues, and final approaches.

---

## 🗂 Project Structure

```

kids-accessories-Ecommerce/
├── Backend/
│   ├── index.js           # Express server entry point
│   └── ...                # API logic, routes, DB config, etc.
├── Frontend/
│   ├── dist/              # Built Vite app (to be served)
│   └── ...                # Vue/React/Vite source code

````

---

## 🧾 Goal

Deploy the **frontend build output (`dist/`)** to an **EC2-hosted Express backend**, such that:

- The backend serves static frontend content.
- The same server handles both frontend and backend functionality.
- You avoid using too much EC2 storage (Git full clone was a problem).

---

## 🧱 Step-by-Step Deployment Guide

---

### ✅ Step 1: Build Frontend Locally

```bash
cd kids-accessories-Ecommerce/Frontend
npm run build
````

This builds your Vite app into a production-ready `dist/` directory.

> 🛑 **Issue Faced:**
>
> ```
> FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed
> ```
>
> **Cause:** Node.js memory not enough for build process.



---

### ❌ Step 2: Attempted `git pull` on EC2

Originally planned to run:

```bash
git pull origin main
```

> ❌ **Problem:**
>
> * Frontend is large and slows down the EC2 instance.
> * Not enough disk space to clone/pull full project.

---

### ❌ Step 3: Tried `git sparse-checkout` to pull only Backend

Used commands:

```bash
git clone --filter=blob:none --no-checkout <repo-url>.git
cd <repo-name>
git sparse-checkout init --cone
git sparse-checkout set kids-accessories-Ecommerce/Backend
```

> ❌ **Issue:**
> Git removed unexpected files. Didn't keep `node_modules` or previously added `dist/`.

---

### ✅ Step 4: Send Only `dist/` Using `scp`

From local PC:

```bash
scp -i ~/Downloads/yene-kids.pem -r dist/ ubuntu@<EC2-IP>:~/kids-accessories-Ecommerce/Backend/Frontend
```

* `-i`: Specifies your EC2 `.pem` key.
* `-r`: Recursive copy (since `dist/` is a folder).
* `scp`: Secure copy over SSH.
* You are sending the Vite build only.

> ❌ **Problem:**
>
> * Slow transfer (took \~20 min) because of file count + upload speed.
> * Doubts about whether `scp` was still working.

✅ **Tip:** Use `rsync` for faster/resumable uploads:

```bash
rsync -avz -e "ssh -i ~/Downloads/yene-kids.pem" dist/ ubuntu@<EC2-IP>:~/kids-accessories-Ecommerce/Backend/Frontend
```

---

### ✅ Step 5: Backend Configuration (`index.js`)

**Before:**

```js
const app = express();
```

**After:**

```js
const path = require("path");

// Serve static files from dist
app.use(express.static(path.join(__dirname, "Frontend", "dist")));

// Catch-all route to serve index.html for frontend routing
app.get("*", (req, res) => {
  res.sendFile(path.join(__dirname, "Frontend", "dist", "index.html"));
});
```

> ✅ **Now:** Visiting EC2 IP or domain shows the deployed frontend.

---

### ✅ Step 6: Bonus Option – Transfer Entire Backend Manually

Instead of Git clone:

#### On Local:

```bash
tar -czf backend.tar.gz Backend
scp -i ~/Downloads/yene-kids.pem backend.tar.gz ubuntu@<EC2-IP>:~/
```

#### On EC2:

```bash
tar -xzf backend.tar.gz
mv Backend kids-accessories-Ecommerce/
rm backend.tar.gz
```

> ✅ **Why this works:**
> Faster, cleaner, avoids Git storage issues.

---

## 🔍 Summary of Lessons Learned

| Step | Task                | Issue                      | Resolution           |
| ---- | ------------------- | -------------------------- | -------------------- |
| 1    | Build frontend      | Memory error               | Use `NODE_OPTIONS`   |
| 2    | Git pull on EC2     | Low space                  | Avoid full clone     |
| 3    | Git sparse-checkout | Files removed unexpectedly | Avoid for EC2 setup  |
| 4    | `scp dist/`         | Slow transfer              | Wait or use `rsync`  |
| 5    | Backend config      | Needed to serve `dist/`    | Use `express.static` |
| 6    | Deploy backend      | Avoid Git                  | Used `tar` + `scp`   |

---



