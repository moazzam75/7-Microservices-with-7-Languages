
# 7 Microservices with 7 Languages

A polyglot microservices application built using seven different programming languages, seven backend services, PostgreSQL, and a React frontend.

The project demonstrates how multiple independent services can work together to support common application workflows such as authentication, product management, inventory, order processing, payments, notifications, and analytics.

---

## Project Overview

This application follows a microservices architecture in which application responsibilities are divided into smaller, independent backend services.

Each backend service is developed using a different programming language and runs on its own port. PostgreSQL is used for persistent data storage, with a separate database for each service.

A React frontend provides the user interface and communicates with the backend services through HTTP APIs.

The seven backend services are:

1. Authentication Service - Java
2. Catalog Service - Go
3. Inventory Service - Node.js
4. Order Service - Python
5. Payment Service - C# / .NET
6. Notification Service - Ruby
7. Analytics Service - PHP

The frontend uses React and Vite.

The project can be deployed locally on Ubuntu or a compatible Linux environment without requiring Docker.

## Project Objectives

The main objectives of this project are to:

- Understand microservices architecture and service separation.
- Run multiple backend services developed in different languages.
- Integrate services through HTTP-based APIs.
- Use PostgreSQL for persistent storage.
- Maintain separate databases for individual services.
- Understand dependencies between services.
- Implement an end-to-end application workflow.
- Automate local service startup and shutdown using shell scripts.
- Check service availability using health-check scripts.
- Practice application deployment and troubleshooting in a Linux environment.

## Technology Stack

| Component | Technology | Port |
|---|---|---:|
| Frontend | React + Vite | 5173 |
| Authentication | Java + Spring Boot | 8081 |
| Product Catalog | Go | 8082 |
| Inventory Management | Node.js | 8083 |
| Order Management | Python + FastAPI | 8084 |
| Payment Processing | C# + ASP.NET Core | 8085 |
| Notifications | Ruby + Sinatra | 8086 |
| Analytics | PHP | 8087 |
| Database | PostgreSQL | 5432 |

**Infrastructure and tools:**

- Ubuntu / Linux
- Git
- Maven
- Go modules
- npm
- Python virtual environments and pip
- .NET SDK
- RubyGems and Bundler
- PostgreSQL
- Bash shell scripts

## Architecture

The application uses a polyglot microservices architecture. Each service has its own responsibility, runtime, port, and database.

### High-level architecture

```text
                       USER
                         |
                         v
                 +----------------+
                 |  React Frontend|
                 |    Port 5173   |
                 +----------------+
                         |
             +-----------+-----------+
             |           |           |
             v           v           v
        +---------+ +----------+ +-----------+
        |   Auth  | |  Catalog | | Inventory |
        |  Java   | |    Go    | |  Node.js  |
        |  :8081  | |   :8082  | |   :8083   |
        +---------+ +----------+ +-----------+
             |           |           |
             v           v           v
          auth_db    catalog_db  inventory_db

                         |
                         v
                 +----------------+
                 | Order Service  |
                 | Python/FastAPI |
                 |    Port 8084   |
                 +----------------+
                    |     |     |
                    |     |     +------------------+
                    v     v                        v
               +---------+  +----------------+ +-------------+
               | Catalog |  |   Inventory    | | Notification|
               |   API   |  |      API       | |    API      |
               +---------+  +----------------+ +-------------+

                         |
                         v
                 +----------------+
                 | Payment Service|
                 |   C# / .NET    |
                 |    Port 8085   |
                 +----------------+
                    |           |
                    v           v
                  Orders    Notifications

                         |
                         v
                 +----------------+
                 | Analytics      |
                 | PHP            |
                 | Port 8087      |
                 +----------------+
                    |    |    |
                    v    v    v
                 Catalog Inventory Orders
                       Payments

    All seven backend services use separate PostgreSQL databases.
```

The diagram shows the main logical relationships. The actual calls depend on the application workflow and API implementation.

## What Does Each Microservice Do?

### 1. Authentication Service

**Technology:** Java + Spring Boot  
**Port:** `8081`  
**Database:** `auth_db`

Responsibilities:

- User registration and login.
- Authentication-related operations.
- Session token management.
- User information lookup.

### 2. Catalog Service

**Technology:** Go  
**Port:** `8082`  
**Database:** `catalog_db`

Responsibilities:

- Product creation, retrieval, updating, and deletion.
- Product search.
- Product details and pricing.
- Providing authoritative product information to other services.

### 3. Inventory Service

**Technology:** Node.js  
**Port:** `8083`  
**Database:** `inventory_db`

Responsibilities:

- Checking available stock.
- Increasing or decreasing inventory.
- Reserving stock for orders.
- Releasing reserved stock when required.

### 4. Order Service

**Technology:** Python + FastAPI  
**Port:** `8084`  
**Database:** `order_db`

Responsibilities:

- Creating and retrieving orders.
- Coordinating product and inventory operations.
- Obtaining product details and prices from the Catalog Service.
- Requesting inventory reservations.
- Managing order lifecycle and status updates.
- Triggering notifications.

### 5. Payment Service

**Technology:** C# + .NET  
**Port:** `8085`  
**Database:** `payment_db`

Responsibilities:

- Processing simulated payments.
- Validating payment amounts against order information.
- Managing payment records.
- Handling refunds.
- Updating order status and triggering notifications when appropriate.

**Note:** This project demonstrates payment workflows. It should not be treated as a production payment-processing system without additional security, validation, and payment-provider integration.

### 6. Notification Service

**Technology:** Ruby  
**Port:** `8086`  
**Database:** `notification_db`

Responsibilities:

- Maintaining notification records.
- Receiving notifications from other services.
- Providing a notification inbox.
- Supporting notification read-status operations.

### 7. Analytics Service

**Technology:** PHP  
**Port:** `8087`  
**Database:** `analytics_db`

Responsibilities:

- Collecting information from other backend services.
- Generating application summary metrics.
- Displaying information such as order counts, revenue, average order value, and low-stock counts.
- Saving analytics snapshots.

---

## Application Flow

The application combines several independent services to complete common business operations.

### 1. User Authentication

1. The user opens the React frontend.
2. The frontend sends a login or registration request to the Authentication Service.
3. The Authentication Service validates the request and manages authentication-related data.
4. The frontend uses the authentication response to continue the application workflow.

### 2. Product Browsing

1. The user views the product catalog.
2. The frontend requests product information from the Catalog Service.
3. The Catalog Service retrieves the relevant data from `catalog_db`.
4. The frontend displays the product details and prices.
5. Inventory information can be obtained separately from the Inventory Service.

### 3. Order Creation

This is the main service-to-service workflow.

1. The user selects products and submits an order.
2. The frontend sends the order request to the Order Service.
3. The Order Service requests product details and authoritative prices from the Catalog Service.
4. The Order Service requests stock reservation from the Inventory Service.
5. The Order Service stores the order in `order_db`.
6. A notification can be sent to the Notification Service.

The Order Service coordinates these operations rather than requiring the frontend to manage every backend interaction itself.

### 4. Payment Processing

1. A payment request is sent to the Payment Service.
2. The Payment Service validates the order and payment amount.
3. The payment operation updates the relevant payment and order records.
4. The order status can change to `PAID` when payment succeeds.
5. A notification is sent to the Notification Service.

The project also demonstrates refund handling and the corresponding order-status and notification updates.

### 5. Notifications

1. Other services send notification requests when relevant events occur.
2. The Notification Service stores notification records in `notification_db`.
3. The frontend or another client can retrieve notifications.
4. Notification read status can be updated where supported.

### 6. Analytics

1. The Analytics Service requests information from relevant backend APIs.
2. It aggregates information for reporting.
3. The resulting summary can include order counts, revenue, average order value, and low-stock information.
4. Analytics snapshots can be saved in `analytics_db`.

### End-to-end flow summary

```text
User
 |
 v
React Frontend
 |
 +----> Authentication Service
 |
 +----> Catalog Service
 |
 +----> Inventory Service
 |
 +----> Order Service
 |         |
 |         +----> Catalog Service
 |         |
 |         +----> Inventory Service
 |         |
 |         +----> Notification Service
 |
 +----> Payment Service
 |         |
 |         +----> Order Service
 |         |
 |         +----> Notification Service
 |
 +----> Notification Service
 |
 +----> Analytics Service
           |
           +----> Catalog Service
           +----> Inventory Service
           +----> Order Service
           +----> Payment Service
```

**Important:** Each service owns its own database. Services communicate through APIs rather than directly sharing another service's database.

---

## Repository Structure

The main project directories are organized as follows:

```text
7-Microservices-with-7-Languages/
|
+-- database/
|   +-- bootstrap.sql
|
+-- frontend/
|   +-- package.json
|   +-- ...
|
+-- services/
|   +-- auth-service/
|   +-- catalog-service/
|   +-- inventory-service/
|   +-- order-service/
|   +-- payment-service/
|   +-- notification-service/
|   +-- analytics-service/
|
+-- scripts/
|   +-- start-all.sh
|   +-- stop-all.sh
|   +-- health-check.sh
|
+-- README.md
+-- LICENSE
```

This is a high-level representation of the repository. Individual service directories contain their own application code and dependency files.

## Prerequisites

The following instructions assume Ubuntu or a compatible Debian-based Linux environment.

### 1. Install the required software

Update package information:

```bash
sudo apt update
```

Install the required packages:

```bash
sudo apt install -y \
  postgresql postgresql-contrib \
  openjdk-21-jdk maven \
  golang-go \
  python3 python3-venv python3-pip \
  ruby-full build-essential libpq-dev \
  php-cli php-pgsql php-curl \
  curl git
```

Install Node.js 22:

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```

Install the .NET 8 SDK:

```bash
sudo apt install -y dotnet-sdk-8.0
```

If the .NET package is unavailable for your Ubuntu release, configure the appropriate Microsoft package repository before installing it.

### 2. Verify installations

Run the following commands:

```bash
java -version
mvn -version
go version
python3 --version
node --version
npm --version
dotnet --version
ruby --version
php --version
psql --version
```

Make sure all required runtimes and tools are installed before proceeding.

---

## Local Deployment: Manual Method

Follow these steps to run the application locally.

### Step 1: Clone the repository

```bash
git clone https://github.com/moazzam75/7-Microservices-with-7-Languages.git

cd 7-Microservices-with-7-Languages
```

### Step 2: Start PostgreSQL

Enable PostgreSQL at system startup and start the service:

```bash
sudo systemctl enable --now postgresql
```

Check its status:

```bash
sudo systemctl status postgresql
```

If your environment does not support `systemctl`, use the service-management command appropriate to that environment.

### Step 3: Create the databases

Run the database bootstrap script from the project root:

```bash
sudo -u postgres psql -f database/bootstrap.sql
```

Verify the databases:

```bash
sudo -u postgres psql -c "\l"
```

The application requires a separate PostgreSQL database for each backend service:

- `auth_db`
- `catalog_db`
- `inventory_db`
- `order_db`
- `payment_db`
- `notification_db`
- `analytics_db`

Use the database credentials configured by the bootstrap script and the individual services. Do not assume credentials from another repository are valid for this project.

### Step 4: Install application dependencies

Install the dependencies for each service from the project root.

#### Java: Authentication Service

```bash
cd services/auth-service
mvn clean package -DskipTests
cd ../..
```

#### Go: Catalog Service

```bash
cd services/catalog-service
go mod tidy
cd ../..
```

#### Node.js: Inventory Service

```bash
cd services/inventory-service
npm install
cd ../..
```

#### Python: Order Service

Create and activate a virtual environment:

```bash
cd services/order-service

python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip setuptools wheel
pip install -r requirements.txt

deactivate
cd ../..
```

#### .NET: Payment Service

```bash
cd services/payment-service
dotnet restore
cd ../..
```

#### Ruby: Notification Service

Configure the Ruby gem environment for the installed Ruby version.

The following example uses the Ruby 3.3 gem directory:

```bash
cd services/notification-service

export GEM_HOME="$HOME/.local/share/gem/ruby/3.3.0"
export GEM_PATH="$GEM_HOME"
export PATH="$GEM_HOME/bin:$PATH"

gem install bundler

bundle config set --local path "$HOME/.bundle"
bundle install

cd ../..
```

To persist these environment variables for future Bash sessions:

```bash
echo 'export GEM_HOME="$HOME/.local/share/gem/ruby/3.3.0"' >> ~/.bashrc
echo 'export GEM_PATH="$GEM_HOME"' >> ~/.bashrc
echo 'export PATH="$GEM_HOME/bin:$PATH"' >> ~/.bashrc

source ~/.bashrc
```

**Note:** Change the Ruby version in `GEM_HOME` if your installed Ruby version uses a different gem directory.

#### PHP: Analytics Service

No additional Composer dependencies are required by this setup.

Verify the required PHP extensions:

```bash
php -m | grep -E "pdo_pgsql|curl"
```

Make sure the PostgreSQL and cURL extensions are available.

#### React: Frontend

```bash
cd frontend
npm install
cd ..
```

### Step 5: Start all services manually

Run each command from the project root. The following commands start the applications in the background and write their output to log files.

#### 1. Authentication Service: Java, port 8081

```bash
cd services/auth-service
nohup mvn spring-boot:run > auth_output.log 2>&1 &
cd ../..
```

#### 2. Catalog Service: Go, port 8082

```bash
cd services/catalog-service
nohup go run . > catalog_output.log 2>&1 &
cd ../..
```

#### 3. Inventory Service: Node.js, port 8083

```bash
cd services/inventory-service
nohup npm start > inventory_output.log 2>&1 &
cd ../..
```

#### 4. Order Service: Python, port 8084

```bash
cd services/order-service
source .venv/bin/activate

nohup uvicorn app.main:app \
  --host 0.0.0.0 \
  --port 8084 \
  --reload > order_output.log 2>&1 &

deactivate
cd ../..
```

#### 5. Payment Service: .NET, port 8085

```bash
cd services/payment-service
nohup dotnet run > payment_output.log 2>&1 &
cd ../..
```

#### 6. Notification Service: Ruby, port 8086

```bash
cd services/notification-service
nohup bundle exec ruby app.rb > notification_output.log 2>&1 &
cd ../..
```

#### 7. Analytics Service: PHP, port 8087

```bash
cd services/analytics-service
nohup php -S 0.0.0.0:8087 router.php > analytics_output.log 2>&1 &
cd ../..
```

#### 8. Frontend: React, port 5173

```bash
cd frontend
nohup npm run dev -- --host 0.0.0.0 > ui_output.log 2>&1 &
cd ..
```

The services run as background processes. Their output is captured in the specified log files.

### Step 6: Open the application

Open the frontend in your browser:

```text
http://localhost:5173
```

Make sure PostgreSQL and all required backend services are running.

---

## Local Deployment: Automated Method

The repository includes shell scripts to simplify local deployment.

### Start all services

From the project root:

```bash
cd scripts
chmod +x start-all.sh stop-all.sh health-check.sh
./start-all.sh
```

The startup script automates the service startup process defined in the script.

### Check service health

Run:

```bash
./health-check.sh
```

This checks the services using the health-check logic implemented in the script.

### Stop all services

Run:

```bash
./stop-all.sh
```

This invokes the process-shutdown logic defined in the script.

**Important:** Start PostgreSQL and complete the database setup and dependency installation before running the application startup script.

---

## Verify the Services

The following commands are examples of how to verify the backend services, provided that each service implements the corresponding `/health` endpoint.

```bash
curl http://localhost:8081/health
curl http://localhost:8082/health
curl http://localhost:8083/health
curl http://localhost:8084/health
curl http://localhost:8085/health
curl http://localhost:8086/health
curl http://localhost:8087/health
```

A successful response indicates that the corresponding endpoint is responding. The exact response body depends on the implementation.

You can also inspect listening ports:

```bash
sudo ss -lntp
```

### Check application logs

The manual deployment commands create logs in their respective service directories.

For example:

```bash
tail -f services/auth-service/auth_output.log
tail -f services/catalog-service/catalog_output.log
tail -f services/inventory-service/inventory_output.log
tail -f services/order-service/order_output.log
tail -f services/payment-service/payment_output.log
tail -f services/notification-service/notification_output.log
tail -f services/analytics-service/analytics_output.log
tail -f frontend/ui_output.log
```

Use these logs to investigate startup failures, dependency issues, database connection errors, and API errors.

## Application URLs

| Service | URL |
|---|---|
| React Frontend | http://localhost:5173 |
| Authentication | http://localhost:8081 |
| Catalog | http://localhost:8082 |
| Inventory | http://localhost:8083 |
| Orders | http://localhost:8084 |
| Payments | http://localhost:8085 |
| Notifications | http://localhost:8086 |
| Analytics | http://localhost:8087 |

For the FastAPI Order Service, the interactive API documentation is normally available at:

http://localhost:8084/docs

The root URL of a backend service may not return a page. Use the API routes implemented by that service.

---

## Troubleshooting

### 1. PostgreSQL connection errors

Check PostgreSQL status:

```bash
sudo systemctl status postgresql
```

Verify the databases exist:

```bash
sudo -u postgres psql -c "\l"
```

Confirm that the credentials and connection settings match the project's database bootstrap script and service configuration.

### 2. Port already in use

Check which process is listening on a port:

```bash
sudo ss -lntp
```

Stop or reconfigure the conflicting process before starting the application.

### 3. A service fails to start

Inspect the relevant service log:

```bash
tail -n 100 services/order-service/order_output.log
```

Replace the path with the appropriate log file for the service that failed.

### 4. Python dependencies are missing

Activate the Order Service virtual environment and reinstall its dependencies:

```bash
cd services/order-service
source .venv/bin/activate
pip install -r requirements.txt
```

### 5. Ruby Bundler errors

Verify the installed Ruby and Bundler versions:

```bash
ruby --version
bundle --version
```

Confirm that `GEM_HOME`, `GEM_PATH`, and `PATH` point to the correct directories for your Ruby installation.

### 6. Frontend cannot connect to backend services

Check that the required backend services are running and that the frontend API configuration points to the correct host and ports.

If the frontend is accessed from another computer, `localhost` refers to that computer, not the machine hosting the backend. Update the frontend API configuration and network access rules as necessary.

### 7. Automated startup or shutdown does not work

Check script permissions:

```bash
chmod +x scripts/start-all.sh
chmod +x scripts/stop-all.sh
chmod +x scripts/health-check.sh
```

Run the scripts from the correct directory and inspect their output for errors.

---

## Learning Outcomes

This project provides practical experience with:

- Polyglot microservices development.
- Java, Go, Node.js, Python, C#, Ruby, and PHP.
- REST API communication between backend services.
- PostgreSQL database provisioning and service-specific data storage.
- Order processing and inventory coordination.
- Payment and refund workflows.
- Notification and analytics integration.
- Linux-based application deployment.
- Dependency installation and runtime configuration.
- Background processes, log management, and health checks.
- Shell-script automation for starting and stopping services.

## License

See the [LICENSE](LICENSE) file in this repository for license information.
