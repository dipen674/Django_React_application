# DevConnector - Dockerized Social Network for Developers

A full-stack social networking platform for developers built with Django REST Framework backend and React frontend, containerized with Docker for easy deployment and development.

## Table of Contents

- [Features](#features)
- [Technology Stack](#technology-stack)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
  - [Option 1: Docker Setup](#option-1-docker-setup-quick--easy-)
  - [Option 2: Manual Setup](#option-2-manual-setup-traditional-method)
- [Quick Start](#quick-start)
- [Project Structure](#project-structure)
- [Container Architecture](#container-architecture)
- [Dockerfile Configurations](#dockerfile-configurations)
- [Development Workflow](#development-workflow)
- [Container Management](#container-management)
- [Troubleshooting](#troubleshooting)
- [Docker Commands Reference](#docker-commands-reference)
- [Support](#support)
- [License](#license)
- [Contributing](#contributing)

## Technology Stack

**Backend:**
- Django REST Framework
- Python 3.9
- SQLite Database

**Frontend:**
- React
- Redux for state management
- Proxy middleware for API communication

**DevOps:**
- Docker & Docker Compose
- Multi-container orchestration

## Prerequisites

### Required Software

**For Docker Setup (Recommended):**
- **Docker** (version 20.10+)
- **Docker Compose** (version 2.0+)
- **Git** (to clone the repository)

**For Manual Setup:**
- Python 3.9+
- Node.js 16+
- npm or yarn
- Git


### Verify Installation

**Docker:**
```bash
# Check Docker version
docker --version

# Check Docker Compose version
docker compose version
```

**Manual Setup:**
```bash
# Check Python version
python --version

# Check Node.js version
node --version

# Check npm version
npm --version
```

## Installation

You can set up DevConnector in two ways:
1. **Docker Setup (Recommended)** - Automated, consistent environment
2. **Manual Setup** - Traditional installation method

### Option 1: Docker Setup (Quick & Easy) 🐳

This is the recommended approach as it eliminates dependency conflicts and environment setup issues.

#### 1. Clone the Repository

```bash
git clone https://github.com/dipen674/Django_React_application.git
cd Django_React_application
```

#### 2. Build and Start with Docker

```bash
# Build and start all services
docker compose up --build

# Or run in detached mode (background)
docker compose up -d --build
```

#### 3. Access the Application

- **Frontend:** http://localhost:3000
- **Backend API:** http://localhost:8000


#### 4. Stop the Application

```bash
# Stop all services
docker compose down
```

**That's it! No manual dependency installation needed.** 🎉

---

### Option 2: Manual Setup (Traditional Method)

This method requires manual installation of all dependencies and configuration.

#### Backend Setup (Django)

1. **Clone the repository:**
```bash
git clone https://github.com/dipen674/Django_React_application.git
cd Django_React_application/backend
```

2. **Install Python dependencies:**
```bash
pip install -r requirements.txt
```

3. **Configure GitHub API (Optional):**

Edit `backend/mysite/settings.py` and add your GitHub credentials:
```python
GIT_CLIENT_ID = 'your github client id'
GIT_CLIENT_SECRET = 'your github client secret'
```

To get GitHub credentials:
- Go to [GitHub Developer Settings](https://github.com/settings/developers)
- Create a new OAuth App
- Copy the Client ID and Client Secret

4. **Run database migrations:**
```bash
python manage.py makemigrations
python manage.py migrate
```

5. **Start the Django server:**
```bash
python manage.py runserver
```

Backend will be available at `http://127.0.0.1:8000`

#### Frontend Setup (React)

1. **Navigate to frontend directory:**
```bash
cd frontend
```

2. **Install Node.js dependencies:**
```bash
npm install
```

3. **Configure API URL:**

The frontend uses proxy configuration. You can either:
- Use the default proxy in `package.json`: `http://127.0.0.1:8000`
- Or set `axios.defaults.baseURL = 'https://api.example.com'` globally

4. **Start the React development server:**
```bash
npm start
```

Frontend will be available at `http://localhost:3000`

5. **For production build:**
```bash
npm run build
```

### Why Choose Docker? 🤔

**Manual Setup:**
- ❌ Install Python, Node.js, and all dependencies manually
- ❌ Potential version conflicts across different machines
- ❌ Need to run multiple terminal windows
- ❌ Complex environment configuration
- ❌ "Works on my machine" problems

**Docker Setup:**
- ✅ One command to start everything (`docker compose up`)
- ✅ Consistent environment across all machines
- ✅ All services run together automatically
- ✅ No dependency installation needed
- ✅ Easy to share and deploy

---

## Quick Start

**Using Docker (Recommended):**

```bash
# Clone and navigate
git clone https://github.com/dipen674/Django_React_application.git
cd Django_React_application

# Start everything
docker compose up --build

# Access the application
# Frontend: http://localhost:3000
# Backend: http://localhost:8000
```

**Manual Setup:**
See the [Manual Setup](#option-2-manual-setup-traditional-method) section for detailed instructions.

## Project Structure

```
DevConnector-Django/
├── backend/                    # Django REST API backend
│   ├── db.sqlite3             # SQLite database file
│   ├── devconnector/          # Main Django application
│   │   ├── admin.py           # Django admin configuration
│   │   ├── apps.py            # App configuration
│   │   ├── migrations/        # Database migration files
│   │   ├── models.py          # Database models
│   │   ├── serializers.py     # DRF serializers
│   │   ├── urls.py            # API endpoint routes
│   │   └── views.py           # API view controllers
│   ├── Dockerfile             # Backend container configuration
│   ├── manage.py              # Django management script
│   ├── mysite/                # Django project settings
│   │   ├── settings.py        # Project configuration
│   │   ├── urls.py            # Main URL routing
│   │   └── wsgi.py            # WSGI server configuration
│   └── requirements.txt       # Python dependencies
│
├── frontend/                   # React frontend application
│   ├── Dockerfile             # Frontend container configuration
│   ├── package.json           # Node.js dependencies
│   ├── public/                # Static public files
│   └── src/                   # React source code
│       ├── actions/           # Redux action creators
│       ├── components/        # React components
│       ├── reducers/          # Redux reducers
│       ├── store.js           # Redux store configuration
│       └── setupProxy.js      # Proxy configuration for API
│
├── docker-compose.yaml        # Multi-container orchestration
├── LICENSE                    # Project license
└── README.md                  # Project documentation
```

## Container Architecture

### Communication Flow

```
Browser (localhost:3000)
    ↓
Frontend Container (React)
    ↓
Proxy Middleware (/api/*)
    ↓
Backend Container (Django on backend:8000)
    ↓
Django API Response
    ↓
Frontend Container
    ↓
Browser
```

### Key Configuration Files

**Frontend Proxy** (`frontend/src/setupProxy.js`):
```javascript
const { createProxyMiddleware } = require('http-proxy-middleware');

module.exports = function(app) {
  app.use('/api', createProxyMiddleware({
    target: process.env.REACT_APP_API_URL || 'http://localhost:8000',
    changeOrigin: true,
  }));
};
```

**Environment** (`frontend/.env`):
```env
REACT_APP_API_URL=http://backend:8000
```

**CORS Settings** (`backend/mysite/settings.py`):
```python
ALLOWED_HOSTS = ['*']  # Development only
```

### Issues Resolved During Containerization

1. **Database Migration Issues**
   - Problem: `no such table: devconnector_user` error
   - Solution: Added automatic migrations in Dockerfile CMD

2. **Authentication Blocking Registration**
   - Problem: 401 Unauthorized on registration endpoint
   - Solution: Added `permission_classes = [AllowAny]` to UserView

3. **Proxy Configuration Issues**
   - Problem: Frontend couldn't reach backend API
   - Solution: Used environment-based proxy with Docker service names

4. **Network Communication Issues**
   - Problem: ECONNREFUSED errors between containers
   - Solution: Used Docker service names (`backend:8000`) instead of localhost

## Development Workflow

### Testing Individual Services

```bash
# Start only backend
docker compose up backend -d

# Start only frontend
docker compose up frontend -d
```

### Database Operations

```bash
# Create superuser
docker compose exec backend python manage.py createsuperuser

# Run migrations manually
docker compose exec backend python manage.py makemigrations
docker compose exec backend python manage.py migrate

# Access Django shell
docker compose exec backend python manage.py shell

# Check database tables
docker compose exec backend python manage.py dbshell
```

### Accessing Container Shells

```bash
# Access backend container shell
docker compose exec backend bash

# Access frontend container shell
docker compose exec frontend sh

# Check backend dependencies
docker compose exec backend pip list

# Check frontend dependencies
docker compose exec frontend npm list
```

## Container Management

### Monitoring Container Status

```bash
# Check running containers
docker ps

# Check all containers (including stopped)
docker ps -a

# Check container resource usage
docker stats

# View detailed container status
docker compose ps
```

### Viewing Logs

```bash
# View logs from all services
docker compose logs

# Follow logs in real-time
docker compose logs -f

# View logs from specific service
docker compose logs backend
docker compose logs frontend

# View last 50 lines
docker compose logs --tail=50

# View logs with timestamps
docker compose logs -t

# Save logs to file
docker compose logs > app_logs.txt
```

### Testing Connectivity

```bash
# Test backend API directly
curl http://localhost:8000/api/auth

# Test frontend
curl http://localhost:3000

# Check if containers can communicate
docker compose exec frontend curl http://backend:8000/api/auth
docker compose exec frontend curl -I http://backend:8000/api/auth

# Ping backend from frontend
docker compose exec frontend ping backend

#Test public API to see response
docker compose exec frontend curl http://backend:8000/api/profiles
```

## Troubleshooting

### Issue 1: Containers Won't Start

**Symptoms:** `docker compose up` fails immediately

**Solutions:**
```bash
# Check for port conflicts
netstat -tulpn | grep :3000
netstat -tulpn | grep :8000

# Rebuild from scratch
docker system prune -a
docker compose up --build
```

### Issue 2: Database Connection Errors

**Symptoms:** "no such table" or migration errors

**Solutions:**
```bash
# Force recreate containers
docker compose down
docker compose up --force-recreate --build

# Manual migration
docker compose exec backend python manage.py makemigrations
docker compose exec backend python manage.py migrate
```

### Issue 3: Frontend Can't Reach Backend

**Symptoms:** "Proxy error: ECONNREFUSED" in frontend logs

**Solutions:**
```bash
# Check if backend is running
docker compose ps backend

# Test backend directly
curl http://localhost:8000/api/auth

# Check backend logs for errors
docker compose logs backend

# Verify network connectivity
docker compose exec frontend ping backend
```

### Issue 6: Container Resource Exhaustion

**Symptoms:** Containers crash or become unresponsive

**Solutions:**
```bash
# Check resource usage
docker stats

# Clean up unused resources
docker system prune
docker volume prune

# Increase Docker memory allocation in Docker Desktop settings
```

### Issue 6: Authentication/Registration Errors

**Symptoms:** 401 Unauthorized on registration

**Solution:** This has been resolved by adding `AllowAny` permissions to registration endpoints.

## Docker Commands Reference

### Container Management

```bash
# Stop all running containers
docker stop $(docker ps -aq)

# Remove unused containers
docker container prune

# Remove all images
docker image prune

# Clean system (remove unused containers, networks, images)
docker system prune -a
  -f flag to remove forcefully
```

### Debugging

```bash
# Inspect container details
docker inspect <container_name>

# View container logs
docker container logs container_name/Id

# Check container resource limits
docker stats <container_name>

# View container networks
docker network ls
```

## Support

If you encounter issues not covered in this guide:

1. **Check the logs:** `docker compose logs`
2. **Verify Docker is running:** `docker info`
3. **Check system resources:** `docker stats`
4. **Ensure ports are available:** `netstat -tulpn | grep :3000` and `netstat -tulpn | grep :8000`
5. **Try complete rebuild:** `docker compose down -v && docker compose up --build`

For more help, visit the [GitHub repository](https://github.com/dipen674/Django_React_application.git) and open an issue.
----------
Software code is taken from this [GitHub repository](https://github.com/devmahmud/DevConnector-Django) which i have dockerized.
