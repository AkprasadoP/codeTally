# 🎉 Codespaces

I was curious about how Codeforces worked, so I created my own version!  
Welcome to **Codespaces** — your social problem-solving playground! 🧑‍💻✨

---

## ✨ Features

- **✏️ Create Your Own Problems:** Feeling creative? You can also create and manage your own coding problems! 🎨  
- **🔍 Codeforces Problem Scraping:** Enter a Codeforces problem link and watch the magic happen! The problem statement and test cases are automatically parsed and shared across all users in the room. 🌟  
- **🚀 Code Evaluation:** Evaluate code submissions on the fly in a safe and secure sandboxed environment!  
- **👩‍💻 Collaborative Coding:** Team up with your friends and solve problems together in real-time with up to 10 users.  
- **🎤 Group Voice Chat:** Integrated group voice chat for seamless collaboration using WebRTC.  
- **📦 Problem Packages:** Easily store and manage problem packages.  
- **🧮🖼️ Math & Image Rendering:** Supports rendering math equations and images in problem statements.  
- **⚡ Caching:** Lightning-fast caching for problem statements and packages using Redis.  

> Can even be used to host contests completely locally (for the most part).

---

## 🎬 Demo

Check out the demo here 🍿  
*(Hosting is currently down as I lost access to my EC2 instance 😢)*

---

## 🏗️ System Architecture

### High-Level
A (not-so-)little monolithic system that handles everything from scraping to collaborative coding.

### Submission Handling
1. Backend pulls test data from the database (or cache if available).  
2. Every new submission triggers a **Docker container** (Alpine Linux).  
3. Container compiles and runs the code in a sandbox:
   - 2-second time limit  
   - 256 MB memory limit  
   - No internet access  
   - Max 100 processes (to avoid fork bombs)  

---

## 🐳 Inside the Container

**Steps performed:**
1. Compile code and put it in a sandboxed environment with the input data.  
2. Randomly change the root password to prevent access.  
3. Lock environment so code can’t access anything outside its sandbox.  
4. Run code with a 2-second time limit.  
5. Compare program output to expected output, return verdict.  

---

## 🔧 Implementation Details

- **🎤 Group Voice Chat:** WebRTC  
- **🐳 Docker API:** Spawn containers for code evaluation  
- **🤝 Collaboration:** Socket.IO for real-time coding  
- **⛏ Web Scraping:** BeautifulSoup custom scraper for Codeforces  
- **🏖 Sandboxing:** Non-privileged users inside Docker  
- **📦 Problem Packages:** Stored in MongoDB  
- **⚡ Redis Caching:** Cache problem packages & test data  
- **🧮 Math Rendering:** KaTeX  
- **🚫 Rate Limiting:** Added to compilation API  

**Supported Languages:** C++ only for now.

---

## 🎯 Current Objectives

- Make the submission system more scalable & efficient (possibly microservice).  
- Multi-node setup using free-tier EC2 + Kubernetes auto-scaling.  
- Fix recent security issue related to compiling untrusted code.  

---

## 🚀 Usage

> **Prerequisites:** Docker & Redis installed in backend.

```bash
# Clone repository
git clone https://github.com/nubskr/codespace.git
cd codespace

# Install dependencies
npm install

# Pull container images & setup environment variables
sudo ./setup.sh
cd ./server
vim .env

# Example .env
MONGODB_URI=''
AWS_ACCESS_KEY_ID=''
AWS_SECRET_ACCESS_KEY=''
AWS_REGION=''
S3_BUCKET_NAME=''
CONCURRENT_SCRAPING_WORKERS=''
CONCURRENT_SUBMISSION_WORKERS=''

# Start backend
npm start

# Start frontend
cd ..
cd ./frontend
npm start
