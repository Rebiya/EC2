# Deployment Guide for yene-kids-accessories full stack website

**Access URL:** [http://44.202.218.119:3000/](http://44.202.218.119:3000/)

---

## Overview

This document guides you through deploying the backend project to an AWS EC2 instance from your local machine.

---

## 📍 Local Machine Instructions

### 1. Zip the Backend Folder

In your local root directory (where the `Backend/` folder exists), run:

```bash
zip -r backend.zip Backend
```

> This creates a compressed `backend.zip` file containing the entire `Backend` folder and its contents (including the frontend `dist/` folder inside).

---

### 2. Transfer the ZIP to EC2 Instance

Use `scp` with your `.pem` key to securely copy `backend.zip` to your EC2 home directory:

```bash
scp -i ~/Downloads/yene-kids.pem backend.zip ubuntu@44.202.218.119:~/
```

---

## 💻 EC2 Instance Instructions

### 3. SSH into the EC2 Instance

Connect to your EC2 instance:

```bash
ssh -i ~/Downloads/yene-kids.pem ubuntu@44.202.218.119
```

---

### 4. Unzip the Project

Extract the zipped project:

```bash
unzip backend.zip
```

> This will extract the `Backend` folder in your home directory.

---

### 5. Install Node Dependencies

Navigate to the `Backend` directory and install required packages:

```bash
cd Backend
npm install
```

> Ensure the `dist/` folder is properly located inside `Frontend/` within `Backend`.

---

### 6. Start the Server

Run your Node.js server (assuming entry point is `Index.js`):

```bash
node Index.js
```

Or if using `nodemon` for auto-restart on changes:

```bash
npx nodemon Index.js
```

---

## ✅ Optional: Keep the Server Running Continuously

To keep your Node.js app running even after logging out, install and use **pm2**:

```bash
sudo npm install -g pm2
pm2 start Index.js
pm2 save
pm2 startup
```

---

## 📝 Quick Summary of Commands

### Local Machine

```bash
zip -r backend.zip Backend
scp -i ~/Downloads/yene-kids.pem backend.zip ubuntu@44.202.218.119:~/
```

### EC2 Instance

```bash
ssh -i ~/Downloads/yene-kids.pem ubuntu@44.202.218.119
unzip backend.zip
cd Backend
npm install
node Index.js
```

---

