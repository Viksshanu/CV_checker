# Developer Guide

## Project Architecture

### Overview
The Placement CV Screening Platform is built using a three-tier architecture:

1. **Frontend Tier** - React-based user interface
2. **Backend Tier** - Express.js REST API
3. **Database Tier** - MongoDB database

### Key Components

#### Database Models

**User Model**
- Stores student and coordinator information
- Role-based user types
- Password hashing for security

**CV Model**
- Student CV records with file references
- Status tracking (pending, selected, rejected)
- Parsed data structure for future CV data extraction

**Interview Model**
- Interview scheduling and management
- Question-answer tracking
- Rating and feedback system

**Notification Model**
- Real-time notifications for users
- Read/unread status tracking
- Different notification types

### API Architecture

#### Authentication Flow
1. User registers with email and password
2. Password hashed with bcryptjs
3. On login, JWT token generated
4. Token sent with every request in Authorization header
5. Middleware verifies token on protected routes

#### File Upload Flow
1. Student selects CV file
2. File validated (type, size)
3. File stored in `/uploads` directory
4. File path saved in database
5. CV record created with metadata

#### CV Review Workflow
1. Coordinator views pending CVs
2. Selects a CV for review
3. Provides status (selected/rejected) and feedback
4. Notification sent to student automatically
5. If selected, coordinator can schedule interview

#### Interview Workflow
1. Coordinator schedules interview with date/time
2. Student receives notification
3. Coordinator asks questions through system
4. Student submits answers
5. Coordinator rates answers
6. Interview completed with overall feedback

### Data Flow Diagrams

#### CV Upload Process
```
Student
   ↓
Register/Login
   ↓
Upload CV (File)
   ↓
File Validation
   ↓
Store in /uploads
   ↓
Create CV Record
   ↓
Notify Coordinators
   ↓
CV Dashboard (Pending)
```

#### CV Review Process
```
Coordinator
   ↓
View CVs
   ↓
Select CV for Review
   ↓
Provide Feedback
   ↓
Mark as Selected/Rejected
   ↓
Notify Student
   ↓
Student Dashboard Updated
```

## Development Workflow

### Adding a New Feature

1. **Define Database Model** (if needed)
   - Create new file in `/models`
   - Define schema using Mongoose

2. **Create Controller**
   - Add logic in `/controllers`
   - Implement business logic

3. **Create Routes**
   - Add endpoints in `/routes`
   - Link to controller methods

4. **Frontend Components**
   - Create React component
   - Add styling with CSS
   - Integrate API calls

5. **Testing**
   - Test API with Postman
   - Test UI in browser
   - Check edge cases

### Code Structure Best Practices

```javascript
// Controller Example
const functionName = async (req, res) => {
  try {
    // Validation
    // Business Logic
    // Database Operations
    res.json({ message: 'Success', data: result });
  } catch (error) {
    res.status(500).json({ message: 'Error', error: error.message });
  }
};
```

```javascript
// API Service Example
export const serviceName = {
  method1: (params) => api.get('/endpoint', { params }),
  method2: (data) => api.post('/endpoint', data)
};
```

## Testing

### Manual Testing with Postman

#### 1. Register User
```
POST http://localhost:5000/api/auth/register
Body: {
  "name": "Test Student",
  "email": "test@example.com",
  "password": "password123",
  "role": "student",
  "registrationNumber": "CSE001",
  "department": "Computer Science"
}
```

#### 2. Login
```
POST http://localhost:5000/api/auth/login
Body: {
  "email": "test@example.com",
  "password": "password123"
}
Response: { "token": "jwt_token_here" }
```

#### 3. Upload CV
```
POST http://localhost:5000/api/cv/upload
Headers: Authorization: Bearer {token}
Body: form-data with "cv" file
```

#### 4. Get All CVs (Coordinator)
```
GET http://localhost:5000/api/coordinator/cvs
Headers: Authorization: Bearer {token}
Query: ?status=pending
```

#### 5. Review CV
```
POST http://localhost:5000/api/coordinator/review/{cvId}
Headers: Authorization: Bearer {token}
Body: {
  "status": "selected",
  "feedback": "Great CV! Selected for interview"
}
```

## Debugging Tips

### Backend Debugging
1. Check console logs: `npm start` shows all logs
2. Use `console.log()` in controller functions
3. Check MongoDB using MongoDB Compass
4. Verify file uploads in `/backend/uploads`

### Frontend Debugging
1. React Developer Tools browser extension
2. Check browser console for errors
3. Use Network tab to inspect API calls
4. Check localStorage for token and user data

### Common Errors

| Error | Solution |
|-------|----------|
| "CastError" | Invalid MongoDB ObjectId |
| "ValidationError" | Missing required fields |
| "TokenExpiredError" | Token expired, login again |
| "ENOENT: no such file" | File doesn't exist, check path |
| "CORS error" | Check FRONTEND_URL in backend .env |

## Performance Optimization

### Database
- Add indexes on frequently queried fields
- Use projection to fetch only needed fields
- Implement pagination for large datasets

### Frontend
- Code splitting for faster load times
- Lazy loading of components
- Minimize re-renders with React.memo

### Backend
- Cache frequently accessed data
- Implement request rate limiting
- Use compression middleware
- Optimize file upload handling

## Security Considerations

1. **Authentication**
   - Use strong JWT secrets
   - Implement token refresh mechanism
   - Set appropriate token expiration

2. **File Uploads**
   - Validate file types
   - Limit file sizes
   - Scan for malware (future enhancement)

3. **Database**
   - Use connection pooling
   - Implement query validation
   - Sanitize user inputs

4. **API Security**
   - Implement rate limiting
   - Use HTTPS in production
   - Add CORS validation
   - Implement CSRF protection

## Scaling Considerations

### Horizontal Scaling
- Use load balancer (Nginx, HAProxy)
- Multiple backend instances
- Shared MongoDB Atlas

### Database Scaling
- Database indexing
- Read replicas
- Sharding for large datasets

### Caching
- Redis for session management
- Cache frequently accessed data
- Implement CDN for static files

## Deployment Checklist

- [ ] Update environment variables
- [ ] Set secure JWT_SECRET
- [ ] Configure MongoDB Atlas
- [ ] Test all features
- [ ] Set up SSL/HTTPS
- [ ] Configure file upload limits
- [ ] Set up logging and monitoring
- [ ] Create database backups
- [ ] Test file uploads
- [ ] Verify email notifications (if added)

## Resources

- [Express.js Documentation](https://expressjs.com/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [React Documentation](https://react.dev/)
- [JWT Best Practices](https://tools.ietf.org/html/rfc7519)
