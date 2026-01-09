# VideoSphere

> Upload once, publish everywhere. A multi-platform video distribution tool for content creators.

VideoSphere allows you to upload a video once and automatically distribute it to multiple platforms while maintaining cloud backups.

## Features

- **Single Upload, Multiple Platforms**: Upload your video once and distribute to all your platforms
- **Cloud Integration**: Upload from Google Drive or backup to cloud storage
- **Automatic Processing**: Videos are automatically uploaded and then cleaned up from temporary storage
- **Metadata Management**: Set title, description, and other metadata once for all platforms, and save ahead of time
- **Fully Containerized**: Easy deployment with Docker

## Tech Stack

- **Frontend**: Vite + React with TypeScript and Tailwind CSS
- **Backend**: Node.js with Express
- **Database**: MongoDB with Mongoose
- **Storage**: Cloudflare R2 (temporary) + your own various cloud providers (permanent)
- **Deployment**: Docker & Docker Compose

## Prerequisites

Before you begin, ensure you have the following installed:

- [Git](https://git-scm.com/downloads)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/) (Windows, Mac, or Linux)
- A code editor (VS Code recommended)

**Important**: We use Docker Desktop for consistency across all platforms (Windows, Mac, Linux).

## Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/threehappypenguins/VideoSphere.git
cd VideoSphere
```

### 2. Set Up Environment Variables

Copy the example environment file and fill in your credentials:

Bash/zsh:
```bash
cp .env.example .env
```

Powershell:
```powershell
copy .env.example .env
```

Edit `.env` with your actual values:

```bash
# At minimum, change these:
MONGO_INITDB_ROOT_USERNAME=admin
MONGO_INITDB_ROOT_PASSWORD=your_secure_password_here

# Add your API credentials as you obtain them:
R2_ACCOUNT_ID=your_r2_account_id
R2_ACCESS_KEY_ID=your_r2_access_key
# ... etc
```

**Never commit your `.env` file to Git!** It's already in `.gitignore`.

### 3. Start the Development Environment

Make sure Docker Desktop is running, then:

```bash
# From the project root
docker compose up --build
```

This will:
- Build and start MongoDB
- Build and start the backend API (http://localhost:3001)
- Build and start the frontend (http://localhost:5173)

### 4. Verify Everything Works

- Frontend: http://localhost:5173
- Backend Health Check: http://localhost:3001/health
- MongoDB: localhost:27017 (connect with MongoDB Compass if needed)

You should see the Vite + React welcome page and the backend health check should return JSON.

### 5. Connect With MongoDB Compass

Note: `changeme` is the password that was set in the .env
URI (Connection String): `mongodb://admin:changeme@localhost:27017/videosphere?authSource=admin`

## Development Workflow

### Regular Development

```bash
# Start all services
docker compose up

# Start in detached mode (runs in background)
docker compose up -d

# View logs
docker compose logs -f

# Stop all services
docker compose down

# Stop and remove volumes (fresh database)
docker compose down -v
```

### When to use `docker compose up --build`?

- Changed `package.json` (added/removed npm packages)
- Changed `Dockerfile`
- Changed `.dockerignore`
- First time setup
- Something's broken and you want to rebuild fresh

### Making Code Changes

Thanks to Docker volumes, your code changes will hot-reload:

- **Backend**: Changes trigger automatic restart (nodemon)
- **Frontend**: React + Vite with Fast Refresh for instant component updates

No need to restart containers for code changes!

### Running Commands Inside Containers

```bash
# Backend shell
docker compose exec backend sh

# Frontend shell
docker compose exec frontend sh

# MongoDB shell
docker compose exec mongodb mongosh -u admin -p your_password

# If you are on Windows and your password contains special characters
docker compose exec mongodb mongosh -u admin -p 'your_password'
```

### Installing New Dependencies

**Option 1: Inside the container** (recommended)
```bash
docker compose exec backend npm install package-name
docker compose exec frontend npm install package-name
```

**Option 2: Locally then rebuild**
```bash
cd backend
npm install package-name
docker compose up --build backend

# Note: if you do it locally, then you are going to have double the node_modules folders
```

## Platform API Setup

You'll need to set up developer accounts and API credentials for each platform:

### Cloudflare R2 for Development
1. Sign up at [Cloudflare](https://cloudflare.com)
2. Create an R2 bucket
3. Generate API tokens
4. Add to `.env`

### YouTube API for Development
1. Go to Google Cloud Console: https://console.cloud.google.com
2. Create a new project
3. Enable YouTube Data API v3
4. Create OAuth 2.0 credentials (for a Web app)
5. Add developer/tester emails to the OAuth consent screen under "Test users"
6. Add YOUTUBE_CLIENT_ID and YOUTUBE_CLIENT_SECRET to your `.env` file

**Notes for End Users**
- Users of this app do NOT need their own API keys.
- Users will authorize the app via OAuth to upload videos to their YouTube accounts.

*(Add more platforms as implemented)*

### Docker or Container build fails
```bash
# Clean everything and rebuild
docker compose down --volumes --rmi all
docker compose up --build
```

### Port Already in Use

Bash/zsh:
```bash
# Find what's using the port
sudo lsof -i :5173
sudo lsof -i :3001

# Kill the process or change ports in .env
sudo kill -9 <PID>
```

Powershell:
```powershell
netstat -ano | findstr :5173
netstat -ano | findstr :3001

# Kill the process or change ports in .env
taskkill /PID <PID> /F
```

### MongoDB Connection Issues
- Ensure MongoDB container is running: `docker compose ps`
- Check credentials match in `.env`
- Try connecting with MongoDB Compass to verify

### Hot Reload Not Working
- Make sure volumes are properly mounted in `docker-compose.yml`
- On Windows, ensure file sharing is enabled in Docker Desktop settings
- Try: `docker compose restart frontend` or `docker compose restart backend`

## Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Stage your changes: `git add .` or `git add path/to/file1 path/to/file2`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request