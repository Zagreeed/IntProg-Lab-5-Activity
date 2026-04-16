# TypeScript CRUD API

## Tech Stack

- **Runtime:** Node.js
- **Language:** TypeScript 6
- **Framework:** Express 5
- **ORM:** Sequelize 6 with MySQL2
- **Validation:** Joi
- **Auth/Security:** JSON Web Tokens (jsonwebtoken), bcryptjs
- **Dev Tools:** ts-node, nodemon

---

## Prerequisites

- Node.js `>= 18`
- MySQL server running locally (default port `3306`)
- npm

---

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/Zagreeed/IntProg-Lab-5-Activity
cd IntProg-Lab-5-Activity
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure the database

Edit `config.json` in the project root and update the credentials to match your local MySQL setup:

```json
{
  "database": {
    "host": "localhost",
    "port": 3306,
    "user": "root",
    "password": "your_password",
    "database": "typescript_crud_api"
  },
  "jwtSecret": "your_jwt_secret"
}
```

> **Note:** The database (`typescript_crud_api`) will be created automatically on first run if it does not already exist.


### 4. Run in development mode

```bash
npm run start:dev
```

This starts the server with `nodemon` + `ts-node`, watching for file changes. The server will be available at:

```
http://localhost:4000
```

### 5. Build for production

```bash
npm run build
npm start
```

This compiles TypeScript to `dist/` and runs the compiled output.

---

## Project Structure

```
typescript-crud-api/
├── src/
│   ├── _helpers/
│   │   ├── db.ts           # Sequelize initialization and DB connection
│   │   └── role.ts         # Role enum (Admin, User)
│   ├── middleware/
│   │   ├── errorHandler.ts # Global error handler middleware
│   │   └── validateRequest.ts # Joi validation helper
│   ├── users/
│   │   ├── user.model.ts   # Sequelize User model
│   │   ├── user.service.ts # Business logic (CRUD operations)
│   │   └── users.controller.ts # Express routes and schema validation
│   └── server.ts           # App entry point
├── config.json             # Database and JWT config
├── tsconfig.json
└── package.json
```

---

## API Endpoints

All endpoints are prefixed with `/users`.

| Method   | Endpoint      | Description          |
|----------|---------------|----------------------|
| `GET`    | `/users`      | Get all users        |
| `GET`    | `/users/:id`  | Get a user by ID     |
| `POST`   | `/users`      | Create a new user    |
| `PUT`    | `/users/:id`  | Update a user by ID  |
| `DELETE` | `/users/:id`  | Delete a user by ID  |

> **Note:** Password hashes are excluded from all GET responses by default via Sequelize's default scope.

---

## Testing the API

You can test the API using `curl`, Postman, or any HTTP client.

### Create a user — `POST /users`

```bash
curl -X POST http://localhost:4000/users \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Mr",
    "firstName": "John",
    "lastName": "Doe",
    "email": "john.doe@example.com",
    "password": "password123",
    "confirmPassword": "password123",
    "role": "User"
  }'
```

**Expected response:**
```json
{ "message": "User Created" }
```

---

### Get all users — `GET /users`

```bash
curl http://localhost:4000/users
```

---

### Get a user by ID — `GET /users/:id`

```bash
curl http://localhost:4000/users/1
```

---

### Update a user — `PUT /users/:id`

```bash
curl -X PUT http://localhost:4000/users/1 \
  -H "Content-Type: application/json" \
  -d '{
    "firstName": "Jane",
    "password": "newpassword123",
    "confirmPassword": "newpassword123"
  }'
```

**Expected response:**
```json
{ "message": "User updated" }
```

---

### Delete a user — `DELETE /users/:id`

```bash
curl -X DELETE http://localhost:4000/users/1
```

**Expected response:**
```json
{ "message": "User deleted" }
```

---

## Validation Rules

### Create (`POST /users`)

| Field             | Rules                                      |
|-------------------|--------------------------------------------|
| `title`           | Required string                            |
| `firstName`       | Required string                            |
| `lastName`        | Required string                            |
| `email`           | Required, valid email format               |
| `password`        | Required, minimum 6 characters             |
| `confirmPassword` | Required, must match `password`            |
| `role`            | Optional — `"Admin"` or `"User"` (default) |

### Update (`PUT /users/:id`)

All fields are optional. If `password` is provided, `confirmPassword` must also match.

---

## Available Scripts

| Script            | Description                                         |
|-------------------|-----------------------------------------------------|
| `npm run start:dev` | Start dev server with hot reload (nodemon + ts-node) |
| `npm run build`   | Compile TypeScript to `dist/`                       |
| `npm start`       | Run the compiled production build                   |
| `npm test`        | Run the test file via ts-node                       |

---

## User Roles

| Role    | Value   |
|---------|---------|
| Admin   | `Admin` |
| User    | `User`  |

Roles are stored as strings in the database and validated on creation/update.