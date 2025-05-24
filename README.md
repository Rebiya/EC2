# Deployment Guide for yene-kids-accessories full stack website

**Access URL:** [http://44.202.218.119:3000/](http://44.202.218.119:3000/)

---

# Recommended Project Folder Structure

```
Backend/
├── Config/
│   └── db.config.js        # Database connection setup and queries
├── Frontend/
│   └── dist/               # Built frontend static files (React/Vue/Angular build)
├── Routes/
│   └── index.routes.js     # API route handlers
├── Index.js                # Main server entry point (your Express app)
├── package.json            # Node.js dependencies and scripts
└── node_modules/           # Installed node modules (after npm install)
```

**Notes:**

* `Frontend/dist/` contains the **production build** of your frontend app. Build your frontend locally (`npm run build` or equivalent) and make sure the output is copied into this folder before deployment.
* `Config/db.config.js` handles database connection/configuration.
* `Routes/index.routes.js` contains your API endpoints.
* `Index.js` is your backend server that serves both API routes and frontend files.

---

# `Index.js` Server File Explained

```js
const express = require("express");
const cors = require("cors");
const dotenv = require("dotenv");
const path = require("path");
dotenv.config();

const query = require("./Config/db.config");
const expressSanitizer = require("express-sanitizer");

const corsOptions = {
  origin: "*",
  credentials: true,
  preflightContinue: false,
  optionsSuccessStatus: 204,
};

const app = express();

app.use(cors(corsOptions));
app.use(express.json());
app.use(expressSanitizer());

// Serve frontend static files from Frontend/dist
app.use(express.static(path.join(__dirname, "Frontend/dist")));

// Backend API routes at /api
const router = require("./Routes/index.routes");
app.use("/api", router);

// For all other routes, serve the frontend's index.html for SPA routing
app.get("*", (req, res) => {
  res.sendFile(path.join(__dirname, "Frontend/dist", "index.html"));
});

const port = process.env.PORT || 3000;

app.listen(port, "0.0.0.0", (err) => {
  if (err) console.log(err);
  console.log(`successfully listened on port ${port}`);
});

module.exports = app;
```

---

# SCP Deployment Guide

### 1. Prepare your project locally

* Make sure your frontend is built and the output is inside `Backend/Frontend/dist/`.
* Your full project folder should follow the above structure.
* From your local root directory (where `Backend/` exists), compress the backend folder:

```bash
zip -r backend.zip Backend
```

---

### 2. Transfer the zipped backend to the EC2 instance

Use the `scp` command with your `.pem` key to copy the zip file to your EC2 home directory:

```bash
scp -i ~/Downloads/yene-kids.pem backend.zip ubuntu@44.202.218.119:~/
```

---

### 3. SSH into your EC2 instance

```bash
ssh -i ~/Downloads/yene-kids.pem ubuntu@44.202.218.119
```

---

### 4. Unzip the project on EC2

```bash
unzip backend.zip
```

This extracts the full `Backend/` folder with all your source code and frontend build.

---

### 5. Install dependencies

Navigate inside the backend folder and install the Node.js dependencies:

```bash
cd Backend
npm install
```

---

### 6. Start your server

Run your server with:

```bash
node Index.js
```

Or, to have automatic reloads during development (if installed):

```bash
npx nodemon Index.js
```

---

### 7. Optional: Keep your server running after logout

Use `pm2` to daemonize your Node.js server:

```bash
sudo npm install -g pm2
pm2 start Index.js
pm2 save
pm2 startup
```

---

# Summary of Commands

| Location  | Commands                                                                |
| --------- | ----------------------------------------------------------------------- |
| **Local** | `zip -r backend.zip Backend`                                            |
|           | `scp -i ~/Downloads/yene-kids.pem backend.zip ubuntu@44.202.218.119:~/` |
| **EC2**   | `ssh -i ~/Downloads/yene-kids.pem ubuntu@44.202.218.119`                |
|           | `unzip backend.zip`                                                     |
|           | `cd Backend`                                                            |
|           | `npm install`                                                           |
|           | `node Index.js` (or `npx nodemon Index.js`) or sudo npm install -g pm2
                 pm2 start Index.js
                 pm2 save
                 pm2 startup

|             


---


---

