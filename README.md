
## ✅ STEP-BY-STEP DEPLOYMENT GUIDE

### 📍 Local Machine Instructions

#### 1. **Zip the Backend folder**

In your local root directory (where `Backend/` is located):

```bash
zip -r backend.zip Backend
```

📦 This compresses the full `Backend` folder (including the frontend `dist/` inside it) into `backend.zip`.

---

#### 2. **Transfer `backend.zip` to EC2 via `scp`**

Use `scp` with your `.pem` key:

```bash
scp -i ~/Downloads/yene-kids.pem backend.zip ubuntu@44.202.218.119:~/
```

✅ This will copy `backend.zip` to your EC2 home directory: `/home/ubuntu`.

---

### 💻 EC2 Instance Instructions

#### 3. **SSH into your EC2 instance**

```bash
ssh -i ~/Downloads/yene-kids.pem ubuntu@44.202.218.119
```

---

#### 4. **Unzip the project**

Once logged in:

```bash
unzip backend.zip
```

📂 This will extract the `Backend` folder.

---

#### 5. **Install Node dependencies**

Navigate into the backend folder and install packages:

```bash
cd Backend
npm install
```

Make sure your `dist/` folder is correctly placed under `Frontend/` inside `Backend`.

---

#### 6. **Run the server**

If your entry point is `Index.js`, run:

```bash
node Index.js
```

Or, if you're using something like `nodemon`:

```bash
npx nodemon Index.js
```

---

## ✅ Optional: Keep the server running (forever)

To avoid the server shutting down after you exit SSH, use **`pm2`**:

```bash
sudo npm install -g pm2
pm2 start Index.js
pm2 save
pm2 startup
```

---

## 📝 Summary of Commands

### On Local:

```bash
zip -r backend.zip Backend
scp -i ~/Downloads/yene-kids.pem backend.zip ubuntu@44.202.218.119:~/
```

### On EC2:

```bash
ssh -i ~/Downloads/yene-kids.pem ubuntu@44.202.218.119
unzip backend.zip
cd Backend
npm install
node Index.js
```

