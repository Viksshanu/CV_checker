# Testing Guide

## Unit Testing Setup

### Install Testing Dependencies

**Backend**:
```bash
npm install --save-dev jest supertest
```

**Frontend**:
```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

## Backend Testing Examples

### Example: Auth Controller Test

```javascript
// backend/__tests__/authController.test.js
const request = require('supertest');
const express = require('express');
const { register, login } = require('../controllers/authController');

const app = express();
app.use(express.json());
app.post('/auth/register', register);
app.post('/auth/login', login);

describe('Auth Controller', () => {
  test('User registration should succeed', async () => {
    const response = await request(app)
      .post('/auth/register')
      .send({
        name: 'Test User',
        email: 'test@example.com',
        password: 'password123',
        role: 'student'
      });
    
    expect(response.status).toBe(201);
    expect(response.body.message).toBe('User registered successfully');
  });

  test('Duplicate email should fail', async () => {
    // First registration
    await request(app)
      .post('/auth/register')
      .send({
        name: 'Test User',
        email: 'test@example.com',
        password: 'password123',
        role: 'student'
      });

    // Second registration with same email
    const response = await request(app)
      .post('/auth/register')
      .send({
        name: 'Another User',
        email: 'test@example.com',
        password: 'password123',
        role: 'student'
      });
    
    expect(response.status).toBe(400);
    expect(response.body.message).toBe('Email already registered');
  });
});
```

## Frontend Testing Examples

### Example: Login Component Test

```javascript
// frontend/src/__tests__/Login.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import Login from '../components/Login';
import * as api from '../services/api';

jest.mock('../services/api');

test('Login component renders correctly', () => {
  render(<Login />);
  const emailInput = screen.getByPlaceholderText('Email');
  const passwordInput = screen.getByPlaceholderText('Password');
  const loginButton = screen.getByText('Login');
  
  expect(emailInput).toBeInTheDocument();
  expect(passwordInput).toBeInTheDocument();
  expect(loginButton).toBeInTheDocument();
});

test('Submit button works', async () => {
  api.authService.login.mockResolvedValue({
    data: {
      token: 'test-token',
      user: { id: '1', name: 'Test', role: 'student' }
    }
  });

  render(<Login />);
  
  fireEvent.change(screen.getByPlaceholderText('Email'), {
    target: { value: 'test@example.com' }
  });
  fireEvent.change(screen.getByPlaceholderText('Password'), {
    target: { value: 'password123' }
  });
  fireEvent.click(screen.getByText('Login'));

  expect(api.authService.login).toHaveBeenCalledWith(
    'test@example.com',
    'password123'
  );
});
```

## Integration Testing

### API Integration Test

```javascript
// backend/__tests__/integration/cvUpload.test.js
const request = require('supertest');
const app = require('../../server');
const fs = require('fs');
const path = require('path');

describe('CV Upload Integration', () => {
  let token;
  let userId;

  beforeAll(async () => {
    // Register and login user
    const registerRes = await request(app)
      .post('/api/auth/register')
      .send({
        name: 'Test Student',
        email: 'student@test.com',
        password: 'password123',
        role: 'student'
      });

    const loginRes = await request(app)
      .post('/api/auth/login')
      .send({
        email: 'student@test.com',
        password: 'password123'
      });

    token = loginRes.body.token;
    userId = loginRes.body.user.id;
  });

  test('CV upload should succeed with valid file', async () => {
    const testFile = path.join(__dirname, 'sample.pdf');
    
    const response = await request(app)
      .post('/api/cv/upload')
      .set('Authorization', `Bearer ${token}`)
      .attach('cv', testFile);

    expect(response.status).toBe(201);
    expect(response.body.cv.status).toBe('pending');
  });

  test('Invalid file format should be rejected', async () => {
    const testFile = path.join(__dirname, 'sample.txt');
    
    const response = await request(app)
      .post('/api/cv/upload')
      .set('Authorization', `Bearer ${token}`)
      .attach('cv', testFile);

    expect(response.status).toBe(400);
  });
});
```

## End-to-End Testing

### Using Cypress

```bash
npm install --save-dev cypress
npx cypress open
```

```javascript
// cypress/e2e/studentFlow.cy.js
describe('Student CV Upload Flow', () => {
  beforeEach(() => {
    cy.visit('http://localhost:3000');
  });

  it('Student can register and upload CV', () => {
    // Navigate to register
    cy.contains('Register here').click();
    
    // Fill registration form
    cy.get('input[name="name"]').type('Test Student');
    cy.get('input[name="email"]').type('student@test.com');
    cy.get('input[name="password"]').type('password123');
    cy.get('input[name="confirmPassword"]').type('password123');
    cy.get('select[name="role"]').select('student');
    cy.get('button').contains('Register').click();
    
    // Login
    cy.url().should('include', '/login');
    cy.get('input[name="email"]').type('student@test.com');
    cy.get('input[name="password"]').type('password123');
    cy.get('button').contains('Login').click();
    
    // Upload CV
    cy.url().should('include', '/student/dashboard');
    cy.get('input[type="file"]').selectFile('cypress/fixtures/resume.pdf');
    cy.get('button').contains('Upload CV').click();
    
    // Verify upload
    cy.contains('CV uploaded successfully').should('be.visible');
  });
});
```

## Performance Testing

```bash
npm install --save-dev artillery
```

```yaml
# load-test.yml
config:
  target: 'http://localhost:5000'
  phases:
    - duration: 60
      arrivalRate: 10
      name: 'Warm up'
    - duration: 120
      arrivalRate: 50
      name: 'Ramp up'
    - duration: 60
      arrivalRate: 100
      name: 'Spike'

scenarios:
  - name: 'CV Upload Flow'
    flow:
      - post:
          url: '/api/auth/login'
          json:
            email: 'student@test.com'
            password: 'password123'
          capture:
            json: '$.token'
            as: 'token'
      - post:
          url: '/api/cv/upload'
          headers:
            Authorization: 'Bearer {{ token }}'
```

Run load test:
```bash
artillery run load-test.yml
```

## Test Coverage

### Generate Coverage Report

**Backend**:
```bash
jest --coverage
```

**Frontend**:
```bash
npm test -- --coverage
```

## CI/CD Testing

### GitHub Actions Example

```yaml
# .github/workflows/test.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      mongodb:
        image: mongo:5.0
        options: >-
          --health-cmd mongodb-health-check
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 27017:27017

    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'
      
      - name: Install backend dependencies
        run: cd backend && npm install
      
      - name: Run backend tests
        run: cd backend && npm test
        env:
          MONGODB_URI: mongodb://localhost:27017/test
          JWT_SECRET: test-secret
      
      - name: Install frontend dependencies
        run: cd frontend && npm install
      
      - name: Run frontend tests
        run: cd frontend && npm test -- --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v2
```

## Best Practices

1. **Write tests early** - Use TDD approach
2. **Test critical paths** - Focus on user flows
3. **Mock external services** - Don't test third-party APIs
4. **Keep tests isolated** - No dependencies between tests
5. **Use descriptive names** - Clear test descriptions
6. **Maintain test data** - Separate fixtures for consistent testing
7. **Regular cleanup** - Remove old test data

## Running Tests

```bash
# Backend
cd backend
npm test

# Frontend
cd frontend
npm test

# With coverage
npm test -- --coverage

# Watch mode
npm test -- --watch
```
