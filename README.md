# Placement CV Screening Platform

A comprehensive web-based platform that digitizes the college placement CV screening process, enabling students to upload CVs, coordinators to review them, and facilitate interviews all through a single digital system.

## Features

### Student Features
- **User Registration & Authentication**: Secure login with role-based access
- **CV Upload**: Upload CVs according to predefined templates (PDF, DOC, DOCX)
- **Status Tracking**: Real-time tracking of CV review status (Pending, Selected, Rejected)
- **Feedback Reception**: Receive detailed feedback and suggestions for improvement
- **Interview Participation**: Participate in interviews conducted through the platform
- **Notifications**: Automatic notifications about CV status and interview updates

### Coordinator Features
- **Admin Dashboard**: Overview of all submitted CVs with statistics
- **CV Management**: View, filter, and manage all student CVs
- **CV Review**: Mark CVs as selected or rejected with feedback
- **Interview Scheduling**: Schedule interviews with selected students
- **Interview Conduction**: Ask questions and collect answers during interviews
- **Performance Rating**: Rate student responses and provide feedback

## Project Structure

```
resume_checker/
├── backend/
│   ├── models/
│   │   ├── User.js
│   │   ├── CV.js
│   │   ├── Interview.js
│   │   └── Notification.js
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── cvController.js
│   │   ├── coordinatorController.js
│   │   ├── interviewController.js
│   │   └── notificationController.js
│   ├── routes/
│   │   ├── authRoutes.js
│   │   ├── cvRoutes.js
│   │   ├── coordinatorRoutes.js
│   │   ├── interviewRoutes.js
│   │   └── notificationRoutes.js
│   ├── middleware/
│   │   ├── auth.js
│   │   └── upload.js
│   ├── uploads/
│   ├── .env
│   ├── package.json
│   └── server.js
│
├── frontend/
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── Login.js
│   │   │   ├── Register.js
│   │   │   └── Auth.css
│   │   ├── pages/
│   │   │   ├── StudentDashboard.js
│   │   │   ├── StudentDashboard.css
│   │   │   ├── CoordinatorDashboard.js
│   │   │   ├── CoordinatorDashboard.css
│   │   │   ├── Notifications.js
│   │   │   ├── Notifications.css
│   │   │   ├── InterviewSchedule.js
│   │   │   └── InterviewSchedule.css
│   │   ├── services/
│   │   │   └── api.js
│   │   ├── App.js
│   │   ├── App.css
│   │   └── index.js
│   ├── .env
│   └── package.json
│
└── README.md
```

## Technology Stack

### Backend
- **Node.js** - Runtime environment
- **Express.js** - Web framework
- **MongoDB** - Database
- **Mongoose** - ODM for MongoDB
- **JWT** - Authentication
- **Multer** - File upload handling
- **Bcryptjs** - Password hashing

### Frontend
- **React** - UI library
- **React Router** - Navigation
- **Axios** - HTTP client

## Installation & Setup

### Prerequisites
- Node.js (v14+)
- MongoDB (local or cloud)
- npm or yarn

### Backend Setup

1. Navigate to backend directory:
```bash
cd backend
```

2. Install dependencies:
```bash
npm install
```

3. Configure environment variables in `.env`:
```
MONGODB_URI=mongodb://localhost:27017/placement_cv_screening
JWT_SECRET=your_jwt_secret_key_here_change_in_production
PORT=5000
NODE_ENV=development
FRONTEND_URL=http://localhost:3000
UPLOAD_DIR=./uploads
```

4. Start the backend server:
```bash
npm start
```

The backend will run on `http://localhost:5000`

### Frontend Setup

1. Navigate to frontend directory:
```bash
cd frontend
```

2. Install dependencies:
```bash
npm install
```

3. Configure environment variables in `.env`:
```
REACT_APP_API_URL=http://localhost:5000/api
```

4. Start the development server:
```bash
npm start
```

The frontend will open on `http://localhost:3000`

## API Endpoints

### Authentication
- `POST /api/auth/register` - Register a new user
- `POST /api/auth/login` - Login user
- `GET /api/auth/profile` - Get user profile

### CV Management
- `POST /api/cv/upload` - Upload CV (Student)
- `GET /api/cv/my-cv` - Get student's CV (Student)
- `GET /api/cv/download/:cvId` - Download CV

### Coordinator Functions
- `GET /api/coordinator/dashboard` - Get dashboard statistics
- `GET /api/coordinator/cvs` - Get all CVs with filtering
- `POST /api/coordinator/review/:cvId` - Review and mark CV

### Interview Management
- `POST /api/interview/schedule` - Schedule interview
- `POST /api/interview/:interviewId/ask-question` - Ask question
- `POST /api/interview/:interviewId/submit-answer/:questionIndex` - Submit answer
- `POST /api/interview/:interviewId/rate/:questionIndex` - Rate answer
- `POST /api/interview/:interviewId/complete` - Complete interview

### Notifications
- `GET /api/notifications` - Get notifications
- `POST /api/notifications/:notificationId/read` - Mark as read
- `POST /api/notifications/read-all` - Mark all as read

## Usage

### For Students
1. Register as a student with your details
2. Login with your credentials
3. Upload your CV in PDF or DOC format
4. Monitor the status of your CV
5. Receive notifications about reviews and interviews
6. Participate in interviews if your CV is selected

### For Coordinators
1. Register/Login as a coordinator
2. View dashboard with statistics
3. Review pending CVs and provide feedback
4. Select promising candidates or reject with suggestions
5. Schedule interviews with selected students
6. Conduct interviews by asking questions and rating responses

## Security Features

- **JWT Authentication**: Secure token-based authentication
- **Password Hashing**: Passwords hashed with bcryptjs
- **Role-Based Access Control**: Different access levels for students and coordinators
- **File Validation**: Only PDF and DOC files allowed
- **File Size Limit**: 5MB maximum file size

## Future Enhancements

- WebSocket integration for real-time notifications
- Video interview support
- Resume parsing and data extraction
- Advanced analytics and reporting
- Email notifications
- Two-factor authentication
- Interview scheduling with calendar integration
- CV template builder
- Bulk operations for coordinators

## Troubleshooting

### MongoDB Connection Error
Ensure MongoDB is running locally or check your connection string in `.env`

### Port Already in Use
Change the PORT in `.env` if port 5000 is already in use

### File Upload Issues
- Verify file format is PDF, DOC, or DOCX
- Check file size is under 5MB
- Ensure uploads directory has write permissions

### CORS Issues
Frontend URL is configured in backend `.env`. Update if frontend is on different URL

## License

MIT License - feel free to use this project for educational purposes

## Support

For issues or questions, please create an issue in the repository.
