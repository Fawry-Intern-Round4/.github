# Organization Microservices System

## Overview

This repository contains a collection of microservices that make up the organization's system. It includes services for managing users, orders, products, stores, coupons, and notifications, along with an Angular frontend. The system leverages Kubernetes (K8s) with Minikube for local deployment and orchestration, as well as a microservices architecture to achieve modularity and scalability.

### Services Included
- **API Gateway**
- **User API**
- **Order API**
- **Product API**
- **Store API**
- **Coupon API**
- **Notification API**
- **Angular Frontend (Front-End)**

Each microservice has its own Kubernetes configuration files (`deployment.yml` and `service.yml`) for deploying and managing resources.

## Prerequisites

Ensure you have the following installed:
- [Minikube](https://minikube.sigs.k8s.io/docs/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Docker](https://docs.docker.com/get-docker/)
- [Node.js and npm](https://nodejs.org/) (for running the Angular frontend)

## Deployment Instructions

### Step 1: Start Minikube

To start a Minikube cluster, run:

```bash
minikube start
```

### Step 2: Deploy All Services

Place all service directories in the same folder. Use the following script to automatically deploy all services using their respective Kubernetes manifests:

**Deploy Script (`deploy_services.sh`):**

```bash
BASE_DIR="." 
for dir in "$BASE_DIR"/*/; do
  if [ -d "$dir" ]; then
    echo "Deploying service in $dir"
    if [ -f "${dir}service.yml" ] && [ -f "${dir}deployment.yml" ]; then
      echo "Applying Deployment and Service for $dir..."
      kubectl apply -f "$dir/deployment.yml"
      kubectl apply -f "$dir/service.yml"
      echo "Successfully deployed service in $dir."
    else
      echo "Missing service.yml or deployment.yml in $dir."
    fi
  fi
done
echo "Waiting for all services to be deployed..."
kubectl wait --for=condition=available --all deployments --timeout=600s
echo "All services deployed successfully!"
read -p "Press any key to exit..."
```

### Step 3: Run the Deployment Script

Navigate to the folder containing all your service directories and run the script:

```bash
chmod +x deploy_services.sh
./deploy_services.sh
```

### Step 4: Configure the Database for Local Development

To configure the database for local development, update the `application.properties` file in each microservice. Here is an example of configuring it for a local MySQL database:

**Example `application.properties` configuration:**

```properties
# Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/your_database_name
spring.datasource.username=your_db_username
spring.datasource.password=your_db_password

# Hibernate Configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5Dialect
```

Adjust `spring.datasource.url`, `spring.datasource.username`, and `spring.datasource.password` to match your local database setup.

### Step 5: Running the Angular Frontend

To run the Angular frontend locally:

1. Navigate to the `Front-End` directory:
   ```bash
   cd Front-End
   ```

2. Install dependencies using npm:
   ```bash
   npm install
   ```

3. Start the Angular development server:
   ```bash
   ng serve
   ```

The Angular frontend will be accessible at `http://localhost:4200/` by default.

## Accessing the System

The **API Gateway** serves as the main entry point for accessing the microservices. Forward its port locally using the following command:

```bash
kubectl port-forward service/api-gateway 8080:8080
```

Access your APIs via `http://localhost:8080`.


## Microservice Details 
### 1. [API Gateway](https://github.com/Fawry-Intern-Round4/API-Gateway)
- **Description**: Routes requests to other services, handles authentication, and provides centralized entry.

### 2. [User API](https://github.com/Fawry-Intern-Round4/User-Api)
- **Description**: Manages user operations (e.g., registration, login, profile).

### 3. [Order API](https://github.com/Fawry-Intern-Round4/Order-API)
- **Description**: Handles order creation, updates, and retrieval.

### 4. [Product API](https://github.com/Fawry-Intern-Round4/Product-Api)
- **Description**: Provides CRUD operations for products and inventory.

### 5. [Store API](https://github.com/Fawry-Intern-Round4/Store-Api)
- **Description**: Manages store data and related operations.

### 6. [Coupon API](https://github.com/Fawry-Intern-Round4/Coupon-API)
- **Description**: Creates and manages discount coupons.

### 7. [Notification API](https://github.com/Fawry-Intern-Round4/Notfication-API)
- **Description**: Sends notifications (e.g., email, SMS).

### 8. [Angular Frontend (Front-End)](https://github.com/Fawry-Intern-Round4/Front-End)
- **Description**: Web interface for interacting with the microservices.
