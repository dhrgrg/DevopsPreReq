# DevOps Checklist for Containerizing & Deploying a Node.js Backend

## Goal

As a DevOps engineer, your job is **not** to understand the application's business logic. Your responsibility is to understand:

- How the application runs
- What dependencies it needs
- How to package it
- How to configure it
- How to deploy it
- How to monitor it

---

# DevOps Thought Process

```text
Source Code
    ↓
Understand the Application
    ↓
Run Locally
    ↓
Identify Dependencies
    ↓
Containerize
    ↓
Configure
    ↓
Deploy
    ↓
Monitor
```

---

# 1. Understand the Project Structure

Typical project:

```text
backend/
│
├── src/
│   ├── app.js
│   ├── routes/
│   ├── controllers/
│   └── services/
│
├── package.json
├── package-lock.json
├── .env.example
├── README.md
└── Dockerfile (optional)
```

Things to identify:

- Is it a Node.js project?
- Is Docker already configured?
- Is there documentation?
- Is there an environment file?

---

# 2. Read `package.json`

This is the most important file.

Example:

```json
{
  "name": "user-service",

  "scripts": {
    "start": "node src/app.js",
    "dev": "nodemon src/app.js",
    "test": "jest"
  },

  "dependencies": {
    "express": "^5.0.0",
    "mongoose": "^8.0.0"
  }
}
```

## Identify

### Runtime

```
Node.js
```

### Entry Point

```
npm start
↓
node src/app.js
```

### Dependency Installation

```
npm install
or
npm ci
```

### Test Command

```
npm test
```

---

# 3. Determine Node Version

Look for:

- `.nvmrc`
- `package.json`

Example:

```json
"engines": {
    "node": "20.x"
}
```

Choose the correct Docker base image:

```dockerfile
FROM node:20-alpine
```

Never guess the version.

---

# 4. Understand Environment Variables

Look for:

```
.env.example
```

Example:

```text
PORT=3000
DB_HOST=
JWT_SECRET=
REDIS_HOST=
LOG_LEVEL=
```

Understand:

- Database credentials
- Secrets
- API keys
- Ports
- External service URLs

Never bake these into the Docker image.

Inject them during runtime.

---

# 5. Identify Application Port

Look for:

```javascript
app.listen(3000)
```

or

```text
PORT=3000
```

Dockerfile:

```dockerfile
EXPOSE 3000
```

---

# 6. Identify Database

Look inside dependencies.

| Package | Database |
|----------|----------|
| mongoose | MongoDB |
| pg | PostgreSQL |
| mysql2 | MySQL |
| prisma | Depends on configuration |

Questions to ask:

- Where is the database hosted?
- Local?
- Cloud?
- Docker?
- AWS RDS?
- Mongo Atlas?

---

# 7. External Services

Common dependencies:

- Redis
- RabbitMQ
- Kafka
- S3
- SMTP
- Stripe
- OAuth
- Elasticsearch

These usually require environment variables.

Example:

```text
REDIS_HOST
SMTP_HOST
AWS_REGION
AWS_ACCESS_KEY
```

---

# 8. Install Dependencies

Development:

```bash
npm install
```

Production:

```bash
npm ci
```

Why?

- Faster
- Reproducible builds
- Uses package-lock.json

---

# 9. Build Step

### JavaScript

Usually:

```bash
npm install
npm start
```

### TypeScript

Usually:

```bash
npm install
npm run build
```

Produces:

```text
src/
    ↓
TypeScript Compiler
    ↓
dist/
```

Run:

```bash
node dist/index.js
```

Not:

```bash
ts-node
```

---

# 10. Logging

Applications should log to:

```text
stdout
stderr
```

Example:

```javascript
console.log("Application started")
```

Container logs become:

```bash
docker logs

kubectl logs
```

Avoid writing logs to files inside containers.

---

# 11. Health Check

Look for endpoints like:

```text
GET /health
GET /ping
GET /status
```

Used by:

- Docker HEALTHCHECK
- Kubernetes readinessProbe
- Kubernetes livenessProbe

---

# 12. Create Dockerfile

Simple example:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

EXPOSE 3000

CMD ["npm","start"]
```

---

# 13. Multi-stage Dockerfile

```dockerfile
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

FROM node:20-alpine

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

COPY --from=builder /app/dist ./dist

CMD ["node","dist/index.js"]
```

Benefits:

- Smaller image
- More secure
- No build tools in runtime image

---

# 14. Create `.dockerignore`

```text
node_modules
.git
.env
coverage
logs
npm-debug.log
Dockerfile
README.md
```

Benefits:

- Faster builds
- Smaller context
- Prevent secret leakage

---

# 15. Build Docker Image

```bash
docker build -t backend:v1 .
```

---

# 16. Run Container

```bash
docker run \
-p 3000:3000 \
-e DB_HOST=db \
-e JWT_SECRET=secret \
backend:v1
```

---

# 17. Verify Application

Useful commands:

```bash
docker ps
```

```bash
docker logs <container>
```

```bash
curl localhost:3000/health
```

---

# 18. CI Pipeline

Typical flow:

```text
Checkout Code
      ↓
Install Dependencies
      ↓
Run Tests
      ↓
Build Application
      ↓
Build Docker Image
      ↓
Push Image
      ↓
Deploy
```

Example:

```yaml
Checkout
↓
npm ci
↓
npm test
↓
docker build
↓
docker push
↓
Deploy
```

---

# 19. Security Best Practices

Never:

```text
COPY .env
```

Never:

```text
Hardcode passwords
```

Never:

```text
Install without package-lock.json
```

Prefer:

- Non-root user
- Minimal base images (Alpine)
- Multi-stage builds
- Production dependencies only
- Runtime secrets

---

# 20. Runtime Configuration

### Docker Image Contains

- Application code
- Node runtime
- Dependencies

### Runtime Provides

- Environment variables
- Secrets
- Database URLs
- Redis URLs
- Feature flags
- API keys

Never bake runtime configuration into the image.

---

# Production Checklist

| Area | Verify |
|-------|--------|
| Runtime | Correct Node.js version |
| Entry Point | npm start / node dist/index.js |
| Lock File | package-lock.json exists |
| Dependencies | Use npm ci |
| Build | npm run build if required |
| Port | Correct EXPOSE value |
| Environment Variables | Identified |
| Database | Connection details known |
| External Services | Redis, Kafka, SMTP, S3, etc. |
| Secrets | Inject at runtime |
| Logging | stdout/stderr |
| Health Check | /health endpoint |
| Docker | Multi-stage build |
| .dockerignore | Present |
| Security | Non-root user, minimal image |
| Testing | npm test |
| Deployment | Environment-specific configuration |

---

# DevOps Questions to Answer Before Production

- What runtime does the application need?
- Which Node.js version should be used?
- How are dependencies installed?
- Is a build step required?
- How is the application started?
- Which port does it listen on?
- Which environment variables are required?
- Which secrets are needed?
- Which database does it connect to?
- Which external services does it depend on?
- How should it be containerized?
- How can its health be verified?
- How will it be monitored?
- How will it be deployed?
- How will the deployment be automated in CI/CD?
- Is the container secure and production-ready?

---

# Key Takeaway

A DevOps engineer doesn't need to understand every line of application code. Instead, they should understand:

- **How to run the application**
- **How to build it**
- **How to configure it**
- **How to package it**
- **How to deploy it**
- **How to monitor it**
- **How to secure it**
- **How to automate the entire lifecycle**
