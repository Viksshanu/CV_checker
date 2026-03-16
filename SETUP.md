# Setup Instructions

## Quick Start

### 1. Backend Setup

```bash
cd backend
npm install
```

Update `.env` file with your MongoDB connection string and other configuration.

```bash
npm start
```

### 2. Frontend Setup

```bash
cd frontend
npm install
npm start
```

## Default Test Accounts

### Student Account
- Email: student@test.com
- Password: password123

### Coordinator Account
- Email: coordinator@test.com
- Password: password123

## Database Initialization

The application uses MongoDB. You can either:

1. **Use Local MongoDB**
   - Install MongoDB locally
   - Update MONGODB_URI in backend/.env to `mongodb://localhost:27017/placement_cv_screening`

2. **Use MongoDB Atlas (Cloud)**
   - Create account at https://www.mongodb.com/cloud/atlas
   - Create a cluster and database
   - Update MONGODB_URI with your connection string

## File Upload Configuration

- Maximum file size: 5MB
- Supported formats: PDF, DOC, DOCX
- Upload directory: `backend/uploads/`

## Common Issues

### Issue: "Cannot find module"
Solution: Run `npm install` in the respective directory

### Issue: Port 5000/3000 already in use
Solution: Kill the process or change PORT in .env

### Issue: Database connection failed
Solution: Verify MongoDB is running and connection string is correct

## Project Walkthrough

1. **Registration**: Visit http://localhost:3000 and register as student or coordinator
2. **Student Dashboard**: Upload CV and track status
3. **Coordinator Dashboard**: Review CVs and manage interviews
4. **Notifications**: Check real-time notifications for updates

## Next Steps

- Customize CV template requirements
- Add email notifications
- Implement video interviews
- Add advanced analytics
- Deploy to production
