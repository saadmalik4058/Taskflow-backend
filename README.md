# Taskflow – Backend API

A RESTful backend API for **Taskflow**, a modern task management application designed for freelancers, developers, students, and small teams.

Built with Node.js, Express 5, and MongoDB, the API provides secure authentication, project and task management, role-based authorization, data validation, and comprehensive integration testing.

## Repositories

- **Frontend:** [Taskflow Frontend](https://github.com/YOUR_USERNAME/taskflow-frontend)
- **Backend:** [Taskflow Backend](https://github.com/YOUR_USERNAME/taskflow-backend)

## Features

- JWT-based authentication and protected routes
- Password hashing with bcrypt
- Role-based authorization (User / Admin)
- Project and task CRUD operations
- Per-user data isolation and resource ownership checks
- Task filtering, searching, sorting, and pagination
- Input validation using Zod
- Centralized error handling
- API rate limiting and security headers
- MongoDB integration using Mongoose
- Integration testing with Jest and Supertest

## Tech Stack

| Category | Technology |
|---|---|
| Runtime | Node.js |
| Framework | Express 5 |
| Database | MongoDB |
| ODM | Mongoose 9 |
| Validation | Zod 4 |
| Authentication | JWT |
| Password Hashing | bcrypt |
| Security | Helmet, CORS, Express Rate Limit |
| Testing | Jest, Supertest, MongoDB Memory Server |

## Requirements

- Node.js 18 or newer
- MongoDB (local instance or hosted cluster)

**Note:** MongoDB does not need to be installed locally to run the test suite. Tests use MongoDB Memory Server.

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/taskflow-backend.git
```

### 2. Navigate to the project directory

```bash
cd taskflow-backend
```

### 3. Install dependencies

```bash
npm install
```

### 4. Configure environment variables

Create your environment file:

```bash
cp .env.example .env
```

For Windows:

```bash
copy .env.example .env
```

Open `.env` and configure the required variables:

```env
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_secret_key
PORT=3000
NODE_ENV=development
```

Generate a secure JWT secret:

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### 5. Start the development server

```bash
npm run dev
```

The API will be available at:

`http://localhost:3000`

## Available Scripts

| Command | Description |
|---|---|
| `npm start` | Start the production server |
| `npm run dev` | Start the development server with Nodemon |
| `npm test` | Run the complete integration test suite |
| `npm run test:watch` | Run tests in watch mode |

## Environment Variables

| Variable | Required | Default | Description |
|---|---|---|---|
| `MONGO_URI` | Yes | – | MongoDB connection string |
| `JWT_SECRET` | Yes | – | Secret used to sign JWT tokens |
| `PORT` | No | `3000` | Server port |
| `NODE_ENV` | No | `development` | Application environment |
| `CLIENT_URLS` | No | Local origins | Allowed CORS origins |
| `RATE_LIMIT_WINDOW_MS` | No | `900000` | Rate-limit window |
| `RATE_LIMIT_MAX` | No | `500` | Maximum requests per window |
| `AUTH_RATE_LIMIT_MAX` | No | `20` | Maximum failed authentication requests per window |

`.env` is excluded from Git. Use `.env.example` as the configuration template.

## API Reference

**Base URL:** `http://localhost:3000`

### Authentication

Protected endpoints require a Bearer token:

```http
Authorization: Bearer <token>
```

Obtain a token through `POST /login`.

- Token expiration: 1 hour
- Supported roles: `user`, `admin`
- New accounts receive the `user` role by default.
- Users cannot assign themselves an admin role during registration.

### Authentication Endpoints

| Method | Endpoint | Access | Description |
|---|---|---|---|
| POST | `/register` | Public | Register a new account |
| POST | `/login` | Public | Authenticate and receive a JWT |
| GET | `/profile` | User | Get authenticated user information |
| GET | `/users` | Admin | List all users (passwords excluded) |

### Project Endpoints

| Method | Endpoint | Access | Description |
|---|---|---|---|
| POST | `/projects` | User | Create a project |
| GET | `/projects` | User | Get your projects |
| GET | `/projects/:id` | Owner | Get a specific project |
| PUT | `/projects/:id` | Owner | Update project details |
| DELETE | `/projects/:id` | Owner | Delete a project and its associated tasks |

### Task Endpoints

| Method | Endpoint | Access | Description |
|---|---|---|---|
| POST | `/tasks` | Project Owner | Create a task |
| GET | `/tasks` | User | Get your tasks |
| GET | `/tasks/:id` | Owner | Get a specific task |
| PUT | `/tasks/:id` | Owner | Update a task |
| DELETE | `/tasks/:id` | Owner | Delete a task |

### Task Filtering, Searching & Pagination

The `GET /tasks` endpoint supports the following query parameters:

| Parameter | Description | Default |
|---|---|---|
| `status` | Filter by `pending`, `in-progress`, or `completed` | – |
| `priority` | Filter by `low`, `medium`, or `high` | – |
| `search` | Search task titles and descriptions | – |
| `page` | Page number (minimum 1) | `1` |
| `limit` | Results per page (1–100) | `10` |
| `sortBy` | Sort by `createdAt`, `updatedAt`, `title`, `dueDate`, or `priority` | `createdAt` |
| `order` | Sort order: `asc` or `desc` | `desc` |

**Example request:**

```http
GET /tasks?status=pending&priority=high&search=landing&page=1&limit=10
```

## Example API Requests

### Register a User

```http
POST /register
Content-Type: application/json
```

```json
{
  "username": "saad",
  "email": "saad@example.com",
  "password": "password123"
}
```

### Login

```http
POST /login
Content-Type: application/json
```

```json
{
  "email": "saad@example.com",
  "password": "password123"
}
```

### Create a Project

```http
POST /projects
Authorization: Bearer <token>
Content-Type: application/json
```

```json
{
  "name": "Website Redesign",
  "description": "Q3 project"
}
```

### Create a Task

```http
POST /tasks
Authorization: Bearer <token>
Content-Type: application/json
```

```json
{
  "title": "Design the landing page",
  "project": "<project-id>",
  "priority": "high",
  "dueDate": "2026-11-30"
}
```

## Testing

The backend includes **83 integration tests** covering:

- Authentication and authorization
- User and resource ownership
- Project and task CRUD operations
- Input validation
- Pagination
- Cascade deletion

Run the test suite:

```bash
npm test
```

Testing uses Jest, Supertest, and MongoDB Memory Server.

Each test file gets an isolated temporary database, ensuring that tests do not interfere with your development database.

## Project Structure

```text
src/
├── app.js
├── server.js
├── config/
│   ├── cors.js
│   └── database.js
├── controllers/
├── middleware/
│   ├── auth.js
│   ├── authorize.js
│   ├── validate.js
│   ├── errorHandler.js
│   └── rateLimiters.js
├── models/
├── routes/
├── utils/
│   └── generateToken.js
└── validators/

tests/
├── setupEnv.js
├── setupDb.js
├── helpers/
└── *.test.js
```

## Security

- Passwords are hashed using bcrypt.
- JWT authentication protects private endpoints.
- Role-based authorization restricts administrative operations.
- Resource ownership checks prevent unauthorized access to other users' projects and tasks.
- Helmet adds security-related HTTP headers.
- Rate limiting protects API endpoints against excessive requests.
- Zod validates incoming request data.
- Mass-assignment protection prevents clients from modifying restricted fields.
- Search input is escaped to prevent regex pattern injection.

## Known Limitations

- Tasks assigned by other users are not included in the current user's task listing.
- Project and user listing endpoints do not currently support pagination.
- Profile information is derived from the JWT payload.
- Refresh tokens and a logout endpoint are not implemented.
- Role changes do not affect previously issued tokens until they expire.

## License

This project is developed for learning and portfolio purposes.
