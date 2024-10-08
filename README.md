# API Gateway Service

This project is a Spring Boot application that acts as an API Gateway, routing requests to the underlying microservices: Game Service API and Auth-Service API. It serves as a single entry point, improving the efficiency and security of the microservice architecture by handling request forwarding, authentication via JWT, and load balancing.   The gateway handles routing, authentication, and request validation using **JWT (JSON Web Tokens)**. It ensures that secured routes require valid authentication tokens, and forwards requests to the appropriate microservice.

## Features

- Routes API requests to the **[Game Service API](https://github.com/juansebstt/game-api)** and **[Authentication Service API](https://github.com/juansebstt/auth-service)** microservices.
- Secures endpoints using **JWT Authentication**.
- Centralized logging and exception handling for microservices.
- Can be extended to handle more microservices if required.
- **Security**: Enforces security on sensitive routes using custom filters.
- **Exception Handling**: Custom error handling for unauthorized requests.
- **API Documentation**: OpenAPI (Swagger) for documenting available endpoints.

## Technologies Used

- **Java 17**
- **Spring Boot 3.3.3**
- **Spring Cloud Gateway** for API Gateway functionality.
- **Spring Security** to manage and forward JWT tokens.
- **PostgreSQL** for database management (if needed for future persistence).
- **Lombok** to reduce boilerplate code.
- **JWT** for secure authentication

## Prerequisites

- **Java 17 or higher**
- **PostgreSQL** installed and running (for the microservices)
- **Maven** for building the project
- **Postman** or any REST client for testing API endpoints

## Microservice Communication

### Game Service API

- Routes to `/v1/games` requests via the gateway.
- The API Gateway forwards requests for managing game data (CRUD operations) to the Game Service API.
- For example:
    - `GET /games` in the gateway is routed to `GET /v1/games` in the Game Service API.
    - `POST /games` in the gateway is routed to `POST /v1/games` in the Game Service API.

### Authentication API

- The API Gateway handles user registration and authentication through the Auth-Service API.
- Requests to `/auth/login` and `/auth/register` are forwarded to the corresponding Auth-Service endpoints.
- JWT tokens are validated and passed along to the Game Service for secure access.
    - For example:
        - `POST /auth/login` on the gateway is routed to `POST /v1/auth/login` on the Auth-Service API.
        - `POST /auth/register` on the gateway is routed to `POST /v1/auth/register` on the Auth-Service API.

## API Endpoints

The gateway exposes a simplified set of endpoints for the two microservices, providing a unified interface.

### Authentication Endpoints

- **POST /auth/login**: For user login and JWT generation.
- **POST /auth/register**: For user registration.

### Game Management Endpoints

- **GET /games**: Retrieves a list of all games.
- **POST /games**: Adds a new game.
- **GET /games/{id}**: Retrieves a specific game by its ID.
- **PUT /games/{id}**: Updates a specific game by its ID.
- **DELETE /games/{id}**: Deletes a game by its ID.

## Security

- **JWT Authentication**: The API Gateway verifies the JWT token provided by the Auth-Service API and forwards it to the Game Service API.
- The gateway ensures that all requests to the Game Service API are authenticated and authorized.

## Setup Instructions

### Prerequisites

- **Java 17 or higher**
- **PostgreSQL** (if needed for future persistence)
- **Maven** for building the project
- **Postman** or any REST client for testing API endpoints

### 

## Setup Instructions

### 1. Clone the Repository

```bash
git clone <https://github.com/juansebstt/auth-service>
cd api-gateway
```

### 2. Configure the Application

Update the `application.yaml` file with any necessary settings, including ensuring the routes and JWT secret are properly configured.

Example configuration:

```yaml
server:
  port: 8082

spring:
  cloud:
    gateway:
      routes:
        - id: auth-route
          uri: http://localhost:8081
          predicates:
            - Path=/v1/auth/**
        - id: game-route
          uri: http://localhost:8080
          predicates:
            - Path=/v1/games/**
          filters:
            - AuthenticationFilter
```

### 3. Build the Project

```bash
mvn clean install
```
### 4. Run the Application

```bash
mvn spring-boot:run
```

## How It Works

This API Gateway routes requests between the following microservices:

- **Authentication API** (running on `http://localhost:8081`): Handles user registration and login, generating JWT tokens.
- **Game Service API** (running on `http://localhost:8080`): Manages game data with CRUD operations.

### Authentication Flow

1. The gateway intercepts all requests to `/v1/games/**` endpoints and applies the `AuthenticationFilter` to ensure that only requests containing a valid JWT token can access secured routes.
2. The token is validated using the `JwtUtils` service, which extracts the token from the `Authorization` header and checks for expiration.
3. If the token is valid, the request proceeds; otherwise, an **Unauthorized (401)** response is returned.

### Routing

- Requests to `/v1/auth/**` are routed to the **Authentication Service**.
- Requests to `/v1/games/**` are routed to the **Game Service** and require a valid JWT for access.

## API Endpoints

### Game Service API Routes (Secured)

- `GET /v1/games`: Retrieves a list of all games.
- `POST /v1/games`: Adds a new game to the database.
- `GET /v1/games/{id}`: Retrieves a specific game by its ID.
- `PUT /v1/games/{id}`: Updates an existing game by its ID.
- `DELETE /v1/games/{id}`: Deletes a game by its ID.

### Authentication API Routes (Open)

- `POST /v1/auth/register`: Register a new user.
- `POST /v1/auth/login`: Authenticates a user and returns a JWT token.
- `GET /v1/auth`: Retrieves user information based on the provided JWT token.

## Exception Handling

The **AuthenticationFilter** checks for missing or invalid tokens. Unauthorized requests will receive a **401 Unauthorized** response, preventing access to secured routes.

## Development & Contribution

To contribute to this project, follow these steps:

1. Fork the repository.
2. Create a new branch for your feature.
3. Make your changes.
4. Submit a pull request.

## Created by

Juan Sebastian Ibarra