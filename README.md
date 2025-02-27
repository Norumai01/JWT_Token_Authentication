# JWT Token and Authentication
A Spring Boot backend application for user authentication and management with JWT security implementation.

## Required Dependencies

- Spring Boot (Spring Web, Spring Security)
- Spring Data JPA
- Jakarta Validation
- Lombok
- JJWT (JSON Web Token)
- BCrypt Password Encoder
- SLF4J (Logging)

## Requirements

- Java 17 or higher
- Maven
- PostgreSQL/MySQL database (or any compatible database)

## Environment Variables

Make sure to set the following environment variables:

- `CORS_ALLOWED_ORIGIN`: URL of your frontend application (e.g., `http://localhost:3000`)
- Any database connection variables (not visible in provided code)

## How to Run

1. **Clone the repository**

   ```bash
   git clone <repository-url>
   cd honkai-website-backend
   ```

2. **Set up environment variables**

   Create a `.env` file in the project root with the following variables:

   ```
   MYSQL_USER=your_mysql_username
   MYSQL_PASSWORD=your_mysql_password
   CORS_ALLOWED_ORIGIN=http://localhost:3000
   ```

   **For Bash/Terminal:**
   ```bash
   export MYSQL_USER=your_mysql_username
   export MYSQL_PASSWORD=your_mysql_password
   export CORS_ALLOWED_ORIGIN=http://localhost:3000
   ```

   **For IntelliJ IDEA:**
   1. Go to `Run` → `Edit Configurations`
   2. Select your Spring Boot application configuration
   3. Go to the `Environment` tab
   4. Add the following environment variables:
      - `MYSQL_USER=your_mysql_username`
      - `MYSQL_PASSWORD=your_mysql_password`
      - `CORS_ALLOWED_ORIGIN=http://localhost:3000`
   5. Click `Apply` and `OK`

3. **Build the application**

   ```bash
   mvn clean install
   ```

4. **Run the application**

   ```bash
   mvn spring-boot:run
   ```

   Or run the JAR file directly:

   ```bash
   java -jar target/honkai-website-backend-0.0.1-SNAPSHOT.jar
   ```

## Features

### Authentication System

- **User Registration**: Create new user accounts with username, email, and password
- **User Login**: Authentication with either username or email
- **JWT Authorization**: Secure API endpoints with JSON Web Token authentication
- **Password Encryption**: BCrypt password encoding for secure storage

### Security Configuration

- **CORS Support**: Configured to allow specific origins, headers, and methods
- **Stateless Sessions**: No server-side session state management
- **Role-Based Authorization**: Support for different user roles (ADMIN, USER)

### User Management

- **User Profiles**: Basic user information storage with bios
- **User Lookup**: Find users by email or username

### Additional Features

- **Comprehensive Logging**: Detailed activity logging using SLF4J
- **Input Validation**: Request validation using Jakarta Validation
- **Exception Handling**: Proper error response formatting

## API Endpoints

### Public Endpoints

- `POST /auth/login`: Authenticate a user and receive a JWT token
- `POST /auth/createUser`: Register a new user

### Protected Endpoints

- `GET /auth/users`: Get a list of all users (requires authentication)
- `GET /test/{email}`: Test endpoint to find a user by email (requires authentication)
- `GET /`: Hello World test endpoint (requires authentication)

## JWT Implementation Details

The application implements JWT (JSON Web Token) authentication with the following components and flow:

### 1. JWT Service (`JWTService.java`)

- **Token Generation:**
  - Uses JJWT library with HS256 hashing algorithm
  - Creates tokens with a 2-hour expiration time
  - Embeds user email as the subject claim

- **Token Validation:**
  - Extracts email from token
  - Verifies token signature using the secret key
  - Checks token expiration
  - Confirms user email matches the token subject

### 2. JWT Filter (`JwtFilter.java`)

- **Request Processing:**
  - Extends `OncePerRequestFilter` to ensure single execution per request
  - Extracts JWT from the "Authorization" header (format: "Bearer {token}")
  - Validates token format (header.payload.signature)

- **Authentication Flow:**
  - Extracts user email from the token
  - Loads user details via `CustomUserDetailsService`
  - Validates token against user details
  - Creates `UsernamePasswordAuthenticationToken` with user authorities
  - Sets authentication in the `SecurityContextHolder`

### 3. Security Configuration Integration (`SecurityConfig.java`)

- Registers JWT filter in the filter chain before `UsernamePasswordAuthenticationFilter`
- Configures stateless session management
- Sets up authentication provider with custom user details service

### 4. Authentication Controller (`AuthController.java`)

- **Login Endpoint:**
  - Authenticates user credentials via `AuthenticationManager`
  - Generates JWT token upon successful authentication
  - Returns token along with user details in the response

### Authentication Flow

1. Client sends credentials to `/auth/login`
2. Server authenticates credentials
3. Server generates JWT token using the user's email
4. Token is returned to client
5. Client includes token in "Authorization" header for subsequent requests
6. `JwtFilter` intercepts requests and validates tokens
7. Valid tokens grant access to protected resources

This implementation provides secure, stateless authentication suitable for RESTful APIs and modern frontend applications.
