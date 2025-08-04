# User Authentication API - Postman Testing Documentation

## Overview
This documentation provides comprehensive testing instructions for the User Authentication API endpoints using Postman. The API includes user registration, login, and logout functionality with JWT-based authentication.

**Base URL:** `https://internshipassingmentone.onrender.com`

## Prerequisites
- Postman installed (desktop app or web version)
- Basic understanding of HTTP methods and JSON
- No browser testing - all endpoints must be tested via Postman or similar API clients

## Authentication Flow
1. **Register** - Create a new user account
2. **Login** - Authenticate and receive access tokens
3. **Logout** - Invalidate user session (requires authentication)

---

## 1. User Registration

### Endpoint Details
- **URL:** `https://internshipassingmentone.onrender.com/api/v1/users/register`
- **Method:** `POST`
- **Content-Type:** `multipart/form-data`

### Required Fields
| Field Name | Type | Required | Description |
|------------|------|----------|-------------|
| `fullName` | Text | Yes | User's full name |
| `email` | Text | Yes | Valid email address |
| `username` | Text | Yes | Unique username |
| `password` | Text | Yes | User password |
| `avatar` | File | Yes | Profile image file |
| `coverImage` | File | No | Cover image file (optional) |

### Postman Setup Instructions

1. **Create New Request**
   - Click the "+" button to create a new request
   - Set method to `POST`
   - Enter URL: `https://internshipassingmentone.onrender.com/api/v1/users/register`

2. **Configure Body**
   - Go to the **Body** tab
   - Select **form-data** option
   - Add the following key-value pairs:

   | Key | Value | Type |
   |-----|-------|------|
   | `fullName` | John Doe | Text |
   | `email` | john.doe@example.com | Text |
   | `username` | johndoe123 | Text |
   | `password` | SecurePass123! | Text |
   | `avatar` | [Select File] | File |
   | `coverImage` | [Select File] | File |

3. **Expected Response**
   ```json
   {
     "statusCode": 200,
     "data": {
       "_id": "user_id_here",
       "fullName": "John Doe",
       "email": "john.doe@example.com",
       "username": "johndoe123",
       "avatar": "cloudinary_url_here",
       "coverImage": "cloudinary_url_here"
     },
     "message": "User registered Successfully",
     "success": true
   }
   ```

### Common Errors
- **400 Bad Request:** Missing required fields
- **409 Conflict:** User already exists
- **500 Internal Server Error:** Server or database issues

---

## 2. User Login

### Endpoint Details
- **URL:** `https://internshipassingmentone.onrender.com/api/v1/users/login`
- **Method:** `POST`
- **Content-Type:** `application/json`

### Required Fields
| Field Name | Type | Required | Description |
|------------|------|----------|-------------|
| `email` OR `username` | String | Yes | User's email or username |
| `password` | String | Yes | User's password |

### Postman Setup Instructions

1. **Create New Request**
   - Set method to `POST`
   - Enter URL: `https://internshipassingmentone.onrender.com/api/v1/users/login`

2. **Configure Headers**
   - Go to **Headers** tab
   - Add: `Content-Type: application/json`

3. **Configure Body**
   - Go to **Body** tab
   - Select **raw** option
   - Select **JSON** from dropdown
   - Enter JSON payload:

   ```json
   {
     "email": "john.doe@example.com",
     "password": "SecurePass123!"
   }
   ```

   OR using username:

   ```json
   {
     "username": "johndoe123",
     "password": "SecurePass123!"
   }
   ```

4. **Expected Response**
   ```json
   {
     "statusCode": 200,
     "data": {
       "user": {
         "_id": "user_id_here",
         "fullName": "John Doe",
         "email": "john.doe@example.com",
         "username": "johndoe123",
         "avatar": "cloudinary_url_here",
         "coverImage": "cloudinary_url_here"
       },
       "accessToken": "jwt_access_token_here",
       "refreshToken": "jwt_refresh_token_here"
     },
     "message": "USER logged in succesfully",
     "success": true
   }
   ```

### Important Notes
- Save the `accessToken` and `refreshToken` from the response
- Cookies are automatically set by Postman for session management

### Common Errors
- **400 Bad Request:** Missing username/email or password
- **404 Not Found:** User does not exist
- **401 Unauthorized:** Invalid credentials

---

## 3. User Logout

### Endpoint Details
- **URL:** `https://internshipassingmentone.onrender.com/api/v1/users/logout`
- **Method:** `POST`
- **Authentication:** Required (Bearer Token)

### Postman Setup Instructions

1. **Create New Request**
   - Set method to `POST`
   - Enter URL: `https://internshipassingmentone.onrender.com/api/v1/users/logout`

2. **Configure Authorization**
   - Go to **Authorization** tab
   - Select **Bearer Token** from Type dropdown
   - Enter the `accessToken` received from login response

3. **No Body Required**
   - This endpoint doesn't require a request body

4. **Expected Response**
   ```json
   {
     "statusCode": 200,
     "data": {},
     "message": "User Logged Out",
     "success": true
   }
   ```

### Common Errors
- **401 Unauthorized:** Missing or invalid access token
- **403 Forbidden:** Token expired or invalid

---

## 4. Token Refresh

### Endpoint Details
- **URL:** `https://internshipassingmentone.onrender.com/api/v1/users/refresh-token`
- **Method:** `POST`
- **Content-Type:** `application/json`

### Postman Setup Instructions

1. **Create New Request**
   - Set method to `POST`
   - Enter URL: `https://internshipassingmentone.onrender.com/api/v1/users/refresh-token`

2. **Configure Body**
   - Go to **Body** tab
   - Select **raw** and **JSON**
   - Enter:

   ```json
   {
     "refreshToken": "your_refresh_token_here"
   }
   ```

3. **Expected Response**
   ```json
   {
     "statusCode": 200,
     "data": {
       "accessToken": "new_access_token_here",
       "refreshToken": "new_refresh_token_here"
     },
     "message": "Access token refreshed",
     "success": true
   }
   ```

---

## Testing Workflow

### Complete Test Sequence

1. **Test Registration**
   - Send POST request to `/register` with valid user data
   - Verify 200 status code and user data in response
   - Note: Each email/username can only be registered once

2. **Test Login**
   - Send POST request to `/login` with registered credentials
   - Verify 200 status code and tokens in response
   - Save `accessToken` for authenticated requests

3. **Test Logout**
   - Use `accessToken` from login in Authorization header
   - Send POST request to `/logout`
   - Verify 200 status code and success message

4. **Test Invalid Cases**
   - Try login with wrong credentials
   - Try logout without token
   - Try registering duplicate user

### Setting Up Postman Environment

1. **Create Environment**
   - Go to Environments tab
   - Click "Create Environment"
   - Add variables:
     - `baseUrl`: `https://internshipassingmentone.onrender.com`
     - `accessToken`: (will be set dynamically)
     - `refreshToken`: (will be set dynamically)

2. **Use Environment Variables**
   - Replace URLs with `{{baseUrl}}/api/v1/users/register`
   - Use `{{accessToken}}` in Authorization headers

### Automated Token Management

Add this script to the **Tests** tab of your login request:

```javascript
// Extract tokens from response
const responseJson = pm.response.json();

if (responseJson.success && responseJson.data.accessToken) {
    // Set tokens as environment variables
    pm.environment.set("accessToken", responseJson.data.accessToken);
    pm.environment.set("refreshToken", responseJson.data.refreshToken);
    
    console.log("Tokens saved to environment");
}
```

---

## Troubleshooting

### Common Issues

1. **500 Internal Server Error**
   - Check if all required fields are provided
   - Ensure file uploads are properly formatted
   - Verify database connectivity (server-side issue)

2. **Cannot GET /api/v1/users/register**
   - You're using GET instead of POST
   - All these endpoints require POST method

3. **CORS Issues**
   - Not applicable in Postman (browser-specific)
   - Use Postman instead of browser for testing

### Best Practices

1. **Always use Postman** for API testing (not browser)
2. **Save requests** in collections for reusability
3. **Use environment variables** for different deployments
4. **Test error scenarios** along with success cases
5. **Document expected responses** for team reference

---

## Collection Export

To share this API collection:

1. Create a collection named "User Authentication API"
2. Add all requests (Register, Login, Logout, Refresh Token)
3. Export collection as JSON
4. Share with team members
5. Import into their Postman to start testing
6. List of Registered users can only be seen by the admin who have Access to MongoDB Atlas Database

This documentation ensures consistent API testing across all devices and environments using Postman's powerful features.