# Running a Node.js Backend on a Fresh Ubuntu Machine (DevOps Approach)

## Scenario

You have:

- A fresh Ubuntu machine
- No Node.js installed
- No npm installed
- No project dependencies
- Only the application's source code

Your goal is to verify that the application works before containerizing or deploying it.

---

# Step 1: Clone or Copy the Source Code

Clone the repository:

```bash
git clone https://github.com/company/backend.git
```

or copy the source code into the machine.

Move into the project.

```bash
cd backend
```

---

# Step 2: Inspect the Project

List files.

```bash
ls -la
```

You should see something like:

```text
package.json
package-lock.json
README.md
src/
.env.example
```

---

# Step 3: Read the README

Always check the documentation first.

```bash
cat README.md
```

Look for:

- Required Node version
- Installation steps
- Environment variables
- Database setup
- Startup command

---

# Step 4: Determine the Required Node Version

Check:

```bash
cat .nvmrc
```

or

```bash
cat package.json
```

Look for:

```json
"engines": {
    "node": "20.x"
}
```

If nothing is specified, ask the developer or inspect CI/Docker configuration.

---

# Step 5: Install Node.js

Update package index.

```bash
sudo apt update
```

Install prerequisites.

```bash
sudo apt install -y curl
```

Install Node.js (example: Node 20).

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```

Install Node.js.

```bash
sudo apt install -y nodejs
```

Verify installation.

```bash
node -v
```

```bash
npm -v
```

Expected:

```text
v20.x.x
10.x.x
```

---

# Step 6: Verify Project Files

Ensure these files exist:

```text
package.json
package-lock.json
```

If `package-lock.json` exists, prefer:

```bash
npm ci
```

Otherwise:

```bash
npm install
```

---

# Step 7: Install Dependencies

Recommended:

```bash
npm ci
```

or

```bash
npm install
```

This downloads all packages into:

```text
node_modules/
```

---

# Step 8: Check Required Environment Variables

Look for:

```bash
ls
```

Example:

```text
.env.example
```

View it.

```bash
cat .env.example
```

Example:

```text
PORT=3000

DB_HOST=

DB_PORT=

DB_USER=

DB_PASSWORD=

JWT_SECRET=
```

---

# Step 9: Create the Environment File

Copy it.

```bash
cp .env.example .env
```

Edit it.

```bash
nano .env
```

Fill in values.

Example:

```text
PORT=3000

DB_HOST=localhost

DB_PORT=5432

DB_USER=test

DB_PASSWORD=password

JWT_SECRET=mysecret
```

---

# Step 10: Identify Required External Services

Inspect dependencies.

Example:

| Dependency | Service Needed |
|------------|----------------|
| mongoose | MongoDB |
| pg | PostgreSQL |
| mysql2 | MySQL |
| redis | Redis |
| amqplib | RabbitMQ |
| kafkajs | Kafka |

If these services are required, install or connect to them before running the application.

---

# Step 11: Check the Startup Script

Open:

```bash
cat package.json
```

Example:

```json
"scripts": {
    "start":"node src/app.js",
    "dev":"nodemon src/app.js",
    "test":"jest"
}
```

Now you know how to start the application.

---

# Step 12: Build the Application (If Required)

For TypeScript projects:

```bash
npm run build
```

This usually creates:

```text
dist/
```

For plain JavaScript projects, this step may not be needed.

---

# Step 13: Start the Application

Production mode:

```bash
npm start
```

Development mode:

```bash
npm run dev
```

Or directly:

```bash
node src/app.js
```

---

# Step 14: Verify It Started

Look for messages like:

```text
Server started
```

or

```text
Listening on port 3000
```

Check running processes.

```bash
ps -ef | grep node
```

Check listening ports.

```bash
ss -tulpn
```

or

```bash
netstat -tulpn
```

---

# Step 15: Test the Application

If it exposes:

```text
http://localhost:3000
```

Test:

```bash
curl http://localhost:3000
```

Health endpoint:

```bash
curl http://localhost:3000/health
```

or

```bash
curl http://localhost:3000/ping
```

Expected:

```json
{
  "status":"UP"
}
```

---

# Step 16: Run Tests

If available:

```bash
npm test
```

This validates the application before deployment.

---

# Step 17: Check Logs

Application logs:

```bash
npm start
```

or

```bash
tail -f logs/app.log
```

(if the application writes logs to files)

---

# Step 18: Troubleshooting

## Missing Module

Example:

```text
Cannot find module express
```

Solution:

```bash
npm install
```

---

## Port Already in Use

Example:

```text
EADDRINUSE
```

Find the process.

```bash
sudo lsof -i :3000
```

Kill it.

```bash
kill -9 <PID>
```

---

## Database Connection Failed

Example:

```text
ECONNREFUSED
```

Check:

- Database is running
- Hostname
- Username
- Password
- Firewall
- Network

---

## Missing Environment Variables

Example:

```text
JWT_SECRET is undefined
```

Verify:

```bash
cat .env
```

---

## Permission Issues

Fix ownership if needed.

```bash
sudo chown -R $USER:$USER .
```

---

# Step 19: Once Verified

After confirming the application runs successfully:

```text
Source Code
      ↓
Install Node
      ↓
Install Dependencies
      ↓
Configure Environment
      ↓
Run Application
      ↓
Verify Health
      ↓
Containerize
      ↓
Build Docker Image
      ↓
Deploy
```

---

# Complete Workflow

```text
Receive Source Code
        ↓
Read README
        ↓
Inspect package.json
        ↓
Determine Node Version
        ↓
Install Node.js
        ↓
Install Dependencies
        ↓
Create .env
        ↓
Configure Database/Redis/etc.
        ↓
Run npm run build (if required)
        ↓
Run npm start
        ↓
Verify Logs
        ↓
Verify Port
        ↓
Call Health Endpoint
        ↓
Run Tests
        ↓
Application Verified
        ↓
Containerize with Docker
        ↓
Deploy
```

---

# DevOps Quick Checklist

- [ ] Clone repository
- [ ] Read README
- [ ] Identify Node version
- [ ] Install Node.js and npm
- [ ] Verify `package.json`
- [ ] Install dependencies (`npm ci`)
- [ ] Create `.env`
- [ ] Configure database/cache/message broker
- [ ] Run `npm run build` (if required)
- [ ] Run `npm start`
- [ ] Verify logs
- [ ] Verify listening port
- [ ] Test application endpoints (`curl`)
- [ ] Run automated tests (`npm test`)
- [ ] Confirm application is production-ready
- [ ] Proceed to Docker containerization
