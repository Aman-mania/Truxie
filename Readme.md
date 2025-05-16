# **This documentation/guide contains all necessary steps to successfully setup and initialize Truxie's dev application divided in three major sections Shipper Web setup, Backend Server setup and Docker setup.**

# A) Truxie Shipper Web

This repository contains the web application for Truxie's shipper platform.

## Prerequisites

Before you begin, ensure you have the following installed on your system:

- [Node.js](https://nodejs.org/) (v16 or later recommended)
- [Yarn](https://yarnpkg.com/)
- [Git](https://git-scm.com/)

## Setup Instructions

We are using Yarn so try to follow the steps accordingly (npm can be used too).
Follow these steps to set up the project locally:

### 1. Clone the Repository

```bash
git clone <repository-url>
```
cd to that directory/folder

### 2. Install Dependencies
Using yarn:

```bash
yarn install
 ```

### 3. Environment Configuration
Create a .env file in the root directory of the project with the following content:

[Frontend .env drive link](https://drive.google.com/drive/folders/1pq13yHuICuiHJjdAgVoFpg2lV0M0FAGH?usp=drive_link)

Ask any superior for access permission if not accessible with your Truxie email.

### 4. Start the Development Server
Using yarn:

```bash
yarn start
 ```

The application should now be running at http://localhost:3000 (or another port (Generally 3001/3002) if 3000 is already in use).

## Project Structure for reference and verification
```plaintext
truxie-shipper-web/
├── public/            # Static assets
├── src/               # Source code
│   ├── assets/        # Images, fonts, etc.
│   ├── components/    # Reusable UI components
│   ├── pages/         # Application pages
│   ├── services/      # API services
│   ├── utils/         # Utility functions
│   ├── App.jsx        # Main application component
│   └── main.jsx       # Entry point
├── .env               # Environment variables
├── package.json       # Project dependencies and scripts
└── vite.config.js     # Vite configuration
 ```


# B) Truxie Backend Setup Documentation
This guide provides step-by-step instructions for setting up the Truxie backend application on Windows.

## Prerequisites
- Node.js (v16 or higher)
- Yarn package manager
- Docker Desktop for Windows
- Git

## Setup Process
### 1. Clone the Repository
```
git clone https://github.com/
truxiein/truxie-backend.git
cd truxie-backend
```
### 2. Configure Environment Variables

Create a .env file in the root directory of the project with the following content:

[Backend .env drive link](https://drive.google.com/drive/folders/1rJzEaG6UT9lwXTflu3TtRKjrM9rXIr4k?usp=drive_link)

Ask any superior for access permission if not accessible with your Truxie email.

### 3. Start Docker Services
```
docker-compose up -d
```
This starts two images:

- PostgreSQL database
- Truxie-Backend-ULIP simulator

### 4. Install Dependencies (Can be done initailly too)
```
yarn install
```

### 5. Run Database Migrations
```
yarn orm migration:up
```

### 6. Verify Database Migration locally(PostgreSQL)
#### Method 1: Using PSQL Commands
After running migrations with yarn orm migration:up , you can verify them using PostgreSQL command-line tools:
**NOTE:** These are linux commands, try running them in **WSL** or any other linux supporting CLI.
```
# Connect to your database (This will allow you to enter the database and access it using CLI)
psql -h localhost -U root -d truxie-entity

# List all tables in the database use: \dt

# View migrations table to see applied migrations:
SELECT * FROM migrations;

# Examine specific table structure:
\d+ shipper
\d+ driver
\d+ driver_bank_details

# Verify specific column 
changes from migrations
SELECT column_name, 
data_type, 
character_maximum_length 
FROM information_schema.
columns 
WHERE table_name = 
'driver_bank_details';

# Check constraints added by 
migrations
SELECT * FROM 
information_schema.
table_constraints 
WHERE table_name = 'shipper';
```

#### Method 2: Using pgAdmin
1. Open pgAdmin and connect to your database
2. Navigate to your database (truxie-entity)
3. Expand the "Schemas" > "public" > "Tables" section (May vary depending on your system and installation)
4. Check the "migrations" table to see applied migrations
5. Examine table structures to verify column types and constraints

#### Method 3: Using MikroORM CLI
```
# Check pending migrations
yarn orm migration:pending

# Check migration history
yarn orm migration:list

# Create a migration diff 
(to see what would change)
yarn orm migration:create 
--dry-run
```

### 7. Start the Application
```
yarn start:dev
```

## Verification and Testing (Endpoints)
Access the API documentation (Swagger Page)
### Use postman for performing CRUD operations on API endpoints
```
http://localhost:3001/rest/
v1/docs
```

## Other Common Issues and Solutions
### Database Connection Failures
- Issue : Cannot connect to PostgreSQL
- Solution : Verify Docker containers are running with docker ps

### Migration Errors (Expected)

This is the most common error that occurs during setup, please make sure to verify if docker images are running and try again. 
It appears to fix itself automatically when we retry executing them.
```
docker-compose up d
```
And then again execute migration
```
yarn orm migration:up
```
This has solved the problem for most of us, if this issue still persists, contact any peer/superior for help.

**Note:** Ensure database is properly initialized and credentials are correct

### Port Conflicts
- Issue : Port already in use
- Solution : Change the port in .env file or stop conflicting services (Already automated)
### Docker Issues
- Issue : Docker containers fail to start
- Solution : Check Docker Desktop is running and has sufficient resources
### ULIP Simulator Issues
- Issue : Cannot connect to ULIP simulator
- Solution : Verify the simulator container is running on port 8123

# C) Docker initialization and Setup:

## Prerequisites (Overall)
- Docker Desktop for Windows installed and running
- Git installed
- Node.js (v20.17.0 as specified in .nvmrc)
- Yarn package manager

## Step 1: Clone the Repositories (Already done)
```
git clone https://github.com/
truxiein/truxie-backend.git
cd truxie-backend
```
Also clone the ULIP simulator repository in the same parent directory:
```
cd ..
git clone https://github.com/
truxiein/ulip-simulator.git
cd truxie-backend
```

## Step 2: Configure Environment Variables (Already done)

## Step 3: Docker Setup Options
### Option A: Full Docker Setup (Database + ULIP Simulator)
This option runs both PostgreSQL and the ULIP simulator in Docker containers:

```
docker-compose up -d
```
This command:
- Starts a PostgreSQL container with PostGIS extension
- Configures the database with credentials from your .env file
- Mounts a volume for persistent data storage at ./postgres-data
- Starts the ULIP simulator on port 8123


### Option B: ULIP Simulator Only (Database on Host) [You can do both too]
If you already have PostgreSQL installed on your machine:
```
docker-compose -f
docker-compose.windows.yml up
```
This only starts the ULIP simulator container.

## Step 4: Verify Docker Containers (Do this to verify running images)
Check if containers are running:
```
docker ps
```
You should see containers for:
- PostgreSQL database (if using Option A)
- ULIP simulator

## Step 5: Connect to the Database (Use above mentioned steps)

### Install pgAdmin to verify database connection:
- Host: localhost
- Port: 5432
- Username: root
- Password: secret
- Database: truxie-entity

#### Using PSQL Commands (Linux supported CLI)
After running migrations with yarn orm migration:up , you can verify them using PostgreSQL command-line tools:
**NOTE:** These are linux commands, try running them in **WSL** or any other linux supporting CLI.
```
# Connect to your database (This will allow you to enter the database and access it using CLI)
psql -h localhost -U root -d truxie-entity

# List all tables in the database use: \dt
```

## Step 6: Initialize the Application
Install dependencies:
```
yarn install
```
Run database migrations:
```
yarn orm migration:up
```
Start the application:
```
yarn start:dev
```

## Common Issues and Solutions

### Docker Container Issues
Issue : Docker containers fail to start Solution :
- Ensure Docker Desktop is running
- Check for port conflicts (especially 5432 for PostgreSQL)
- Verify Docker has sufficient resources

Issue : Cannot connect to database Solution :
- Check if the database container is running: docker ps
- Verify environment variables in .env match the Docker Compose file
- Try restarting the container: docker-compose restart database

### Database Connection Issues
Issue : Migration fails Solution :
- Ensure database container is running and healthy
- Check database credentials in .env
- Verify database exists: docker exec -it database psql -U root -d truxie-entity -c "\l"

### ULIP Simulator Issues
Issue : Cannot connect to ULIP simulator Solution :
- Verify the simulator container is running: docker ps
- Check if it's accessible at http://localhost:8123
- Rebuild the container if needed: docker-compose build ulip-simulator

### Volume Persistence Issues
Issue : Database data is lost after container restart Solution :
- Check if volume is properly mounted
- Ensure the postgres-data directory has proper permissions
## Docker Commands Reference
```
# Start containers in 
detached mode
docker-compose up -d

# View container logs
docker-compose logs

# Stop containers
docker-compose down

# Rebuild containers
docker-compose build

# Remove volumes (will 
delete database data)
docker-compose down -v
```

## Kindly thoroughly follow this documentation for seamless initialization and setup of Truxie's dev application.
