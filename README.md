# BookTracker Web App

## Setup Instructions

### Prerequisites
- Docker
- Docker Compose

### Running the Application

1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. Start the Docker containers:
   ```bash
   docker-compose up --build
   ```

3. Access the applications:
   - Laravel backend: [http://localhost:8000](http://localhost:8000)
   - Vue.js frontend: [http://localhost:8080](http://localhost:8080)

### Stopping the Application

To stop the application, run:
```bash
docker-compose down
```

### Database Configuration

The MySQL database is configured with the following credentials:
- **Database**: booktracker
- **User**: user
- **Password**: password

These can be changed in the `docker-compose.yml` file.
