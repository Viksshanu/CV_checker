# API Documentation

Base URL: `http://localhost:5000/api`

## Authentication Endpoints

### 1. User Registration
**Endpoint:** `POST /auth/register`

**Request:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "securepassword123",
  "role": "student",
  "registrationNumber": "CSE2023001",
  "department": "Computer Science",
  "phone": "+1234567890"
}
```

**Response:** `201 Created`
```json
{
  "message": "User registered successfully",
  "userId": "507f1f77bcf86cd799439011",
  "role": "student"
}
```

**Error Response:** `400 Bad Request`
```json
{
  "message": "Email already registered"
}
```

---

### 2. User Login
**Endpoint:** `POST /auth/login`

**Request:**
```json
{
  "email": "john@example.com",
  "password": "securepassword123"
}
```

**Response:** `200 OK`
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "name": "John Doe",
    "email": "john@example.com",
    "role": "student"
  }
}
```

**Error Response:** `401 Unauthorized`
```json
{
  "message": "Invalid credentials"
}
```

---

### 3. Get User Profile
**Endpoint:** `GET /auth/profile`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "name": "John Doe",
  "email": "john@example.com",
  "role": "student",
  "registrationNumber": "CSE2023001",
  "department": "Computer Science",
  "phone": "+1234567890",
  "createdAt": "2024-01-15T10:30:00Z"
}
```

---

## CV Management Endpoints

### 1. Upload CV
**Endpoint:** `POST /cv/upload`

**Headers:**
```
Authorization: Bearer {token}
Content-Type: multipart/form-data
```

**Form Data:**
- `cv` (file): PDF or DOC file (max 5MB)

**Response:** `201 Created`
```json
{
  "message": "CV uploaded successfully",
  "cv": {
    "id": "507f1f77bcf86cd799439012",
    "fileName": "resume.pdf",
    "uploadDate": "2024-01-15T10:35:00Z",
    "status": "pending"
  }
}
```

**Error Responses:**
- `400 Bad Request` - No file selected
- `413 Payload Too Large` - File exceeds 5MB
- `415 Unsupported Media Type` - Invalid file format

---

### 2. Get Student's CV
**Endpoint:** `GET /cv/my-cv`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
{
  "_id": "507f1f77bcf86cd799439012",
  "student": {
    "_id": "507f1f77bcf86cd799439011",
    "name": "John Doe",
    "email": "john@example.com"
  },
  "fileName": "resume.pdf",
  "filePath": "/uploads/507f1f77bcf86cd799439011-1705326900000.pdf",
  "fileSize": 245632,
  "uploadDate": "2024-01-15T10:35:00Z",
  "status": "selected",
  "feedback": "Great CV! Selected for next round.",
  "reviewedBy": "507f1f77bcf86cd799439013",
  "reviewDate": "2024-01-15T14:20:00Z"
}
```

**Error Responses:**
- `404 Not Found` - CV not uploaded yet
- `401 Unauthorized` - No valid token

---

### 3. Download CV
**Endpoint:** `GET /cv/download/{cvId}`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK` (File download)

**Error Responses:**
- `404 Not Found` - CV not found
- `403 Forbidden` - Unauthorized access

---

## Coordinator Endpoints

### 1. Get Dashboard Statistics
**Endpoint:** `GET /coordinator/dashboard`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
{
  "stats": {
    "totalCVs": 45,
    "selectedCVs": 12,
    "rejectedCVs": 8,
    "pendingCVs": 25
  },
  "recentCVs": [
    {
      "_id": "507f1f77bcf86cd799439012",
      "student": {
        "name": "John Doe",
        "email": "john@example.com",
        "registrationNumber": "CSE2023001"
      },
      "fileName": "resume.pdf",
      "uploadDate": "2024-01-15T10:35:00Z",
      "status": "pending"
    }
  ]
}
```

---

### 2. Get All CVs (with filtering)
**Endpoint:** `GET /coordinator/cvs`

**Headers:**
```
Authorization: Bearer {token}
```

**Query Parameters:**
- `status` (optional): `pending`, `selected`, or `rejected`

**Example:** `GET /coordinator/cvs?status=pending`

**Response:** `200 OK`
```json
[
  {
    "_id": "507f1f77bcf86cd799439012",
    "student": {
      "_id": "507f1f77bcf86cd799439011",
      "name": "John Doe",
      "email": "john@example.com",
      "registrationNumber": "CSE2023001",
      "department": "Computer Science",
      "phone": "+1234567890"
    },
    "fileName": "resume.pdf",
    "uploadDate": "2024-01-15T10:35:00Z",
    "status": "pending",
    "feedback": null,
    "reviewedBy": null
  }
]
```

---

### 3. Review CV
**Endpoint:** `POST /coordinator/review/{cvId}`

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "status": "selected",
  "feedback": "Excellent profile! Shortlisted for interview."
}
```

**Response:** `200 OK`
```json
{
  "message": "CV reviewed successfully",
  "cv": {
    "_id": "507f1f77bcf86cd799439012",
    "status": "selected",
    "feedback": "Excellent profile! Shortlisted for interview.",
    "reviewedBy": "507f1f77bcf86cd799439013",
    "reviewDate": "2024-01-15T14:20:00Z"
  }
}
```

**Note:** Student receives automatic notification with status and feedback.

---

## Interview Endpoints

### 1. Schedule Interview
**Endpoint:** `POST /interview/schedule`

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "cvId": "507f1f77bcf86cd799439012",
  "scheduledDate": "2024-02-10T10:00:00Z"
}
```

**Response:** `201 Created`
```json
{
  "message": "Interview scheduled successfully",
  "interview": {
    "_id": "507f1f77bcf86cd799439014",
    "cv": "507f1f77bcf86cd799439012",
    "student": "507f1f77bcf86cd799439011",
    "coordinator": "507f1f77bcf86cd799439013",
    "status": "scheduled",
    "scheduledDate": "2024-02-10T10:00:00Z",
    "questions": [],
    "createdAt": "2024-01-15T15:00:00Z"
  }
}
```

---

### 2. Ask Question in Interview
**Endpoint:** `POST /interview/{interviewId}/ask-question`

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "questionText": "Describe your experience with React.js"
}
```

**Response:** `200 OK`
```json
{
  "message": "Question added successfully",
  "interview": {
    "_id": "507f1f77bcf86cd799439014",
    "questions": [
      {
        "questionText": "Describe your experience with React.js",
        "studentAnswer": null,
        "rating": null,
        "feedback": null,
        "askedAt": "2024-02-10T10:05:00Z"
      }
    ]
  }
}
```

**Note:** Student receives notification about new question.

---

### 3. Submit Interview Answer
**Endpoint:** `POST /interview/{interviewId}/submit-answer/{questionIndex}`

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "studentAnswer": "I have 3 years of experience with React.js, working on various projects..."
}
```

**Response:** `200 OK`
```json
{
  "message": "Answer submitted successfully",
  "interview": {
    "questions": [
      {
        "questionText": "Describe your experience with React.js",
        "studentAnswer": "I have 3 years of experience with React.js...",
        "rating": null,
        "feedback": null,
        "askedAt": "2024-02-10T10:05:00Z"
      }
    ]
  }
}
```

---

### 4. Rate Interview Answer
**Endpoint:** `POST /interview/{interviewId}/rate/{questionIndex}`

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "rating": 4,
  "feedback": "Good answer, could have mentioned more about performance optimization."
}
```

**Response:** `200 OK`
```json
{
  "message": "Answer rated successfully",
  "interview": {
    "questions": [
      {
        "questionText": "Describe your experience with React.js",
        "studentAnswer": "I have 3 years of experience with React.js...",
        "rating": 4,
        "feedback": "Good answer, could have mentioned more...",
        "askedAt": "2024-02-10T10:05:00Z"
      }
    ]
  }
}
```

---

### 5. Complete Interview
**Endpoint:** `POST /interview/{interviewId}/complete`

**Headers:**
```
Authorization: Bearer {token}
```

**Request:**
```json
{
  "overallRating": 4,
  "overallFeedback": "Good technical knowledge. Consider working on communication skills."
}
```

**Response:** `200 OK`
```json
{
  "message": "Interview completed successfully",
  "interview": {
    "_id": "507f1f77bcf86cd799439014",
    "status": "completed",
    "overallRating": 4,
    "overallFeedback": "Good technical knowledge...",
    "completedAt": "2024-02-10T10:45:00Z"
  }
}
```

---

### 6. Get Student's Interviews
**Endpoint:** `GET /interview/my-interviews`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
[
  {
    "_id": "507f1f77bcf86cd799439014",
    "cv": {...},
    "coordinator": {
      "_id": "507f1f77bcf86cd799439013",
      "name": "Dr. Smith",
      "email": "smith@college.edu"
    },
    "status": "completed",
    "scheduledDate": "2024-02-10T10:00:00Z",
    "questions": [...],
    "overallRating": 4,
    "overallFeedback": "Good technical knowledge...",
    "completedAt": "2024-02-10T10:45:00Z"
  }
]
```

---

## Notification Endpoints

### 1. Get Notifications
**Endpoint:** `GET /notifications`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
[
  {
    "_id": "507f1f77bcf86cd799439015",
    "type": "cv_status",
    "title": "CV Selected",
    "message": "Your CV has been selected! You will be contacted soon for interview.",
    "read": false,
    "createdAt": "2024-01-15T14:25:00Z"
  },
  {
    "_id": "507f1f77bcf86cd799439016",
    "type": "interview_invite",
    "title": "Interview Scheduled",
    "message": "Your interview has been scheduled for 2024-02-10.",
    "read": true,
    "createdAt": "2024-01-16T09:00:00Z"
  }
]
```

---

### 2. Mark Notification as Read
**Endpoint:** `POST /notifications/{notificationId}/read`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
{
  "_id": "507f1f77bcf86cd799439015",
  "type": "cv_status",
  "title": "CV Selected",
  "read": true
}
```

---

### 3. Mark All Notifications as Read
**Endpoint:** `POST /notifications/read-all`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
{
  "message": "All notifications marked as read"
}
```

---

### 4. Get Unread Count
**Endpoint:** `GET /notifications/unread-count`

**Headers:**
```
Authorization: Bearer {token}
```

**Response:** `200 OK`
```json
{
  "unreadCount": 3
}
```

---

## Error Codes

| Code | Status | Description |
|------|--------|-------------|
| 200 | OK | Request successful |
| 201 | Created | Resource created successfully |
| 400 | Bad Request | Invalid request data |
| 401 | Unauthorized | Missing or invalid token |
| 403 | Forbidden | Access denied |
| 404 | Not Found | Resource not found |
| 413 | Payload Too Large | File too large |
| 415 | Unsupported Media Type | Invalid file format |
| 500 | Internal Server Error | Server error |

---

## Common Response Headers

```
Content-Type: application/json
Access-Control-Allow-Origin: *
```

---

## Rate Limiting

- No rate limiting implemented yet
- Recommended: 100 requests/minute per IP

---

## Authentication

All endpoints except `/auth/register` and `/auth/login` require:

```
Authorization: Bearer {token}
```

Token is valid for 7 days from issue date. Include it in every authenticated request header.
