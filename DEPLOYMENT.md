# Deployment Guide

## Prerequisites
- Docker & Docker Compose (recommended)
- Node.js 16+
- MongoDB Atlas account (for cloud deployment) or local MongoDB

## Option 1: Docker Deployment (Recommended)

### Create docker-compose.yml in root directory

```yaml
version: '3.8'

services:
  mongodb:
    image: mongo:5.0
    container_name: placement_mongo
    environment:
      MONGO_INITDB_DATABASE: placement_cv_screening
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

  backend:
    build: ./backend
    container_name: placement_backend
    environment:
      MONGODB_URI: mongodb://mongodb:27017/placement_cv_screening
      JWT_SECRET: your-secure-secret-key
      PORT: 5000
      NODE_ENV: production
      FRONTEND_URL: http://localhost:3000
    ports:
      - "5000:5000"
    depends_on:
      - mongodb
    volumes:
      - ./backend/uploads:/app/uploads

  frontend:
    build: ./frontend
    container_name: placement_frontend
    environment:
      REACT_APP_API_URL: http://localhost:5000/api
    ports:
      - "3000:3000"
    depends_on:
      - backend

volumes:
  mongo_data:
```

### Build and Run with Docker Compose

```bash
docker-compose up --build
```

## Option 2: Manual Deployment on Ubuntu/Linux

### Install Node.js
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Install MongoDB
```bash
sudo apt-get install -y mongodb
sudo systemctl start mongodb
```

### Clone and Setup Backend

```bash
git clone <your-repo-url>
cd resume_checker/backend
npm install
pm2 start server.js --name "placement-backend"
```

### Setup Frontend

```bash
cd ../frontend
npm install
npm run build
pm2 serve build 3000 --spa --name "placement-frontend"
```

## Option 3: Cloud Deployment (AWS/Heroku)

### Deploy Backend to Heroku

```bash
cd backend
heroku create placement-backend
heroku config:set MONGODB_URI=<your-mongodb-atlas-uri>
heroku config:set JWT_SECRET=<your-secret-key>
git push heroku main
```

### Deploy Frontend to Vercel

```bash
cd frontend
npm install -g vercel
vercel
```

## Environment Variables for Production

### Backend
```
MONGODB_URI=mongodb+srv://user:password@cluster.mongodb.net/db_name
JWT_SECRET=your-very-secure-random-string
PORT=5000
NODE_ENV=production
FRONTEND_URL=https://your-frontend-domain.com
```

### Frontend
```
REACT_APP_API_URL=https://your-backend-domain.com/api
```

## Database Backup

### MongoDB Backup
```bash
mongodump --uri="mongodb+srv://user:password@cluster.mongodb.net/placement_cv_screening" --out=./backup
```

### MongoDB Restore
```bash
mongorestore --uri="mongodb+srv://user:password@cluster.mongodb.net/placement_cv_screening" ./backup
```

## Monitoring

### View Logs
```bash
pm2 logs placement-backend
pm2 logs placement-frontend
```

### Monitor System
```bash
pm2 monit
```

## Updates

### Pull Latest Changes
```bash
git pull origin main
```

### Rebuild Backend
```bash
cd backend
npm install
pm2 restart placement-backend
```

### Rebuild Frontend
```bash
cd frontend
npm install
npm run build
pm2 restart placement-frontend
```
