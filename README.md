# CodeMate Backend API 🚀

> **REST API & Real-Time Server for CodeMate – The Developer Dating App**

This is the backend service for CodeMate, providing RESTful APIs for authentication, user management, connection requests, and real-time chat via Socket.io.

![Node.js](https://img.shields.io/badge/Node.js-18.x-green?logo=node.js)
![Express](https://img.shields.io/badge/Express-5.x-black?logo=express)
![MongoDB](https://img.shields.io/badge/MongoDB-8.x-green?logo=mongodb)
![Socket.io](https://img.shields.io/badge/Socket.io-4.x-black?logo=socket.io)

---

## 🔗 Related Repository

**Frontend Repository:** [CodeMate Web](https://github.com/gauravzyadav/codeMate-web) – React frontend application

---

## ✨ Features

- **JWT Authentication** – Secure signup, login, logout with token-based auth
- **User Profiles** – Create and update developer profiles
- **Connection System** – Send, receive, accept, reject connection requests
- **Real-Time Chat** – Socket.io powered instant messaging
- **Image Uploads** – Cloudinary integration for profile photos
- **Feed Algorithm** – Smart feed that excludes already interacted users

---

## 🛠️ Tech Stack

| Technology | Purpose |
|------------|---------|
| Node.js 18 | Runtime Environment |
| Express 5 | Web Framework |
| MongoDB | Database |
| Mongoose | ODM (Object Document Mapper) |
| JWT | Authentication Tokens |
| bcrypt | Password Hashing |
| Socket.io | Real-Time Communication |
| Cloudinary | Image Storage |
| Multer | File Upload Handling |
| Validator | Input Validation |

---

## 📁 Project Structure

```
devTinder/
├── src/
│   ├── config/
│   │   ├── database.js        # MongoDB connection
│   │   └── cloudinary.js      # Cloudinary configuration
│   │
│   ├── middlewares/
│   │   ├── auth.js            # JWT authentication middleware
│   │   └── upload.js          # Multer file upload config
│   │
│   ├── models/
│   │   ├── user.js            # User schema & methods
│   │   ├── connectionRequest.js # Connection request schema
│   │   └── chat.js            # Chat & message schema
│   │
│   ├── routes/
│   │   ├── auth.js            # /signup, /login, /logout
│   │   ├── profile.js         # /profile/view, /profile/edit
│   │   ├── request.js         # /request/send, /request/review
│   │   ├── user.js            # /user/connections, /feed
│   │   ├── chat.js            # /chat/:targetUserId
│   │   └── upload.js          # /upload/photo
│   │
│   ├── utils/
│   │   ├── socket.js          # Socket.io initialization
│   │   └── validation.js      # Input validation helpers
│   │
│   └── app.js                 # Express app entry point
│
├── package.json
├── vercel.json                # Vercel deployment config
└── .env                       # Environment variables (not committed)
```

---

## 🚀 Getting Started

### Prerequisites

- Node.js 18.x or higher
- MongoDB (local installation or MongoDB Atlas)
- Cloudinary account (free tier works)

### Installation

**1. Clone the repository**
```bash
git clone https://github.com/gauravzyadav/codeMate-backend.git
cd devTinder
```

**2. Install dependencies**
```bash
npm install
```

**3. Create environment file**

Create a `.env` file in the root directory:
```env
PORT=3000
DB_CONNECTION_SECRET=mongodb+srv://username:password@cluster.mongodb.net/codemate
JWT_SECRET=your_super_secret_jwt_key_here
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
NODE_ENV=development
```

**4. Start the server**
```bash
# Development (with hot reload)
npm run dev

# Production
npm start
```

The API will be running at `http://localhost:3000`

---

## 📡 API Documentation

### Base URL
- **Development:** `http://localhost:3000`

---

### Authentication

#### Register User
```http
POST /signup
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "emailId": "john@example.com",
  "password": "SecurePass123!"
}
```

**Response:**
```json
{
  "message": "User Added Successfully!",
  "user": { ... },
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

#### Login
```http
POST /login
Content-Type: application/json

{
  "emailId": "john@example.com",
  "password": "SecurePass123!"
}
```

#### Logout
```http
POST /logout
Authorization: Bearer <token>
```

---

### Profile

#### View Profile
```http
GET /profile/view
Authorization: Bearer <token>
```

#### Edit Profile
```http
PATCH /profile/edit
Authorization: Bearer <token>
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "age": 25,
  "gender": "male",
  "about": "Full-stack developer",
  "photoUrl": "https://..."
}
```

**Allowed fields:** `firstName`, `lastName`, `age`, `gender`, `about`, `photoUrl`

---

### Connection Requests

#### Send Request
```http
POST /request/send/:status/:toUserId
Authorization: Bearer <token>
```
- `status`: `interested` (like) or `ignored` (pass)
- `toUserId`: Target user's MongoDB ID

#### Review Request
```http
POST /request/review/:status/:requestId
Authorization: Bearer <token>
```
- `status`: `accepted` or `rejected`
- `requestId`: Connection request's MongoDB ID

---

### User Data

#### Get Feed (Users to Swipe)
```http
GET /feed?page=1&limit=10
Authorization: Bearer <token>
```
Returns users excluding:
- Current user
- Users already sent/received requests

#### Get Connections (Matches)
```http
GET /user/connections
Authorization: Bearer <token>
```

#### Get Pending Requests
```http
GET /user/requests/received
Authorization: Bearer <token>
```

---

### Chat

#### Get Chat History
```http
GET /chat/:targetUserId
Authorization: Bearer <token>
```

---

### Upload

#### Upload Profile Photo
```http
POST /upload/photo
Authorization: Bearer <token>
Content-Type: multipart/form-data

photo: <file>
```

**Response:**
```json
{
  "photoUrl": "https://res.cloudinary.com/...",
  "publicId": "devtinder/profile_photos/..."
}
```

---

## 💬 Real-Time Chat (Socket.io)

### Connection
```javascript
const socket = io("http://localhost:3000", {
  transports: ['websocket', 'polling'],
  withCredentials: true
});
```

### Events

#### Join Chat Room
```javascript
socket.emit("joinChat", {
  firstName: "John",
  userId: "user_id_here",
  targetUserId: "target_user_id"
});
```

#### Send Message
```javascript
socket.emit("sendMessage", {
  firstName: "John",
  lastName: "Doe",
  userId: "user_id_here",
  targetUserId: "target_user_id",
  text: "Hello!"
});
```

#### Receive Message
```javascript
socket.on("messageReceived", ({ firstName, lastName, text }) => {
  console.log(`${firstName}: ${text}`);
});
```

### How Rooms Work
- Room ID = SHA-256 hash of sorted user IDs
- Ensures both users always join the same room
- Messages are broadcast only to room participants

---

## 🗄️ Database Models

### User
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| firstName | String | Yes | 4-50 characters |
| lastName | String | No | User's last name |
| emailId | String | Yes | Unique, valid email |
| password | String | Yes | Strong password (hashed) |
| age | Number | No | 18-150 |
| gender | String | No | male/female/others |
| photoUrl | String | No | Profile image URL |
| about | String | No | Bio/description |
| skills | [String] | No | Array of skills |
| isPremium | Boolean | No | Premium status |

### ConnectionRequest
| Field | Type | Description |
|-------|------|-------------|
| fromUserId | ObjectId | Sender's user ID |
| toUserId | ObjectId | Receiver's user ID |
| status | String | ignored/interested/accepted/rejected |

### Chat
| Field | Type | Description |
|-------|------|-------------|
| participants | [ObjectId] | Array of two user IDs |
| messages | [Message] | Array of message objects |

### Message (embedded)
| Field | Type | Description |
|-------|------|-------------|
| senderId | ObjectId | Sender's user ID |
| text | String | Message content |
| createdAt | Date | Timestamp |

---

## 🔐 Security

- **Password Hashing:** bcrypt with 10 salt rounds
- **JWT Tokens:** 7-day expiry, stored in HTTP-only cookies
- **Input Validation:** Server-side validation using validator.js
- **CORS:** Configured for specific origins only
- **Strong Passwords:** Enforced during signup

---

## 🌐 Deployment

### Render (Current)
The API is deployed on Render:
```
https://devtinder-backend-yhxb.onrender.com
```

### Vercel (Alternative)
Vercel configuration is included in `vercel.json`

### Environment Variables (Production)
Set these in your hosting platform:
- `PORT`
- `DB_CONNECTION_SECRET`
- `JWT_SECRET`
- `CLOUDINARY_CLOUD_NAME`
- `CLOUDINARY_API_KEY`
- `CLOUDINARY_API_SECRET`
- `NODE_ENV=production`

---

## 📜 Scripts

| Command | Description |
|---------|-------------|
| `npm start` | Start production server |
| `npm run dev` | Start development server with nodemon |

---

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request


---

<p align="center">
  <b>Built with ❤️ for developers who code and connect</b>
</p>
