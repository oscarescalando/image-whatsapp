# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Starting the Application
```bash
npm start
```
Starts the Express server on port 8000 (defined in `src/config/constans.js`).

### Dependencies
```bash
npm install
```
Installs all project dependencies including Baileys, Express, AWS SDK, Puppeteer, and other required packages.

## Architecture Overview

This is a WhatsApp automation API built with Express.js and the Baileys library. The application provides RESTful endpoints to manage WhatsApp connections, send messages, and convert HTML to images.

### Core Components

**Entry Point**: `index.js` - Starts the Express server
**Main App**: `src/app.js` - Express application setup with middleware and routes

### Directory Structure

```
src/
├── config/
│   └── constans.js        # Configuration constants (PORT, file paths)
├── controllers/
│   └── whatsappController.js  # HTTP request handlers
├── routes/
│   └── whatsappRoutes.js     # API route definitions
├── services/
│   └── whatsappService.js    # WhatsApp connection logic using Baileys
└── utils/
    ├── fileManager.js        # File operations utility
    └── socketManager.js      # WhatsApp socket management
```

### Key Features

1. **WhatsApp Connection Management**: Uses Baileys library to establish and manage WhatsApp Web connections
2. **Multi-device Support**: Handles multiple WhatsApp devices simultaneously with unique device IDs
3. **Message Sending**: Supports text messages and images
4. **HTML to Image Conversion**: Uses Puppeteer to convert HTML content to images and upload to AWS S3
5. **Session Persistence**: Maintains WhatsApp authentication sessions across restarts

### External Dependencies

- **AWS S3**: For image storage (requires AWS credentials in environment variables)
- **Puppeteer**: For HTML to image conversion
- **Baileys**: For WhatsApp Web API integration
- **Socket.IO**: For real-time communication

### Environment Variables Required

```
AWS_REGION
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_BUCKET
```

### API Endpoints

The application exposes REST endpoints for:
- QR code generation for WhatsApp authentication
- Sending messages (text and images)
- Device management (connect/disconnect)
- User information retrieval
- HTML to image conversion

### Socket Management

The `socketManager` utility handles:
- Persistent storage of WhatsApp socket connections in `sockets.json`
- Managing active device connections
- Session cleanup on disconnect

### Session Management

WhatsApp authentication sessions are stored in `session_{deviceId}` directories and automatically managed by the Baileys library.

## Docker Deployment

### Building and Running with Docker

```bash
# Build the Docker image
docker build -t whatsapp-api .

# Run the container
docker run -p 8000:8000 \
  -e AWS_REGION=your-region \
  -e AWS_ACCESS_KEY_ID=your-access-key \
  -e AWS_SECRET_ACCESS_KEY=your-secret-key \
  -e AWS_BUCKET=your-bucket-name \
  -v $(pwd)/sessions:/usr/src/app/sessions \
  whatsapp-api
```

### Using Docker Compose

```bash
# Create .env file with your AWS credentials
echo "AWS_REGION=your-region" > .env
echo "AWS_ACCESS_KEY_ID=your-access-key" >> .env
echo "AWS_SECRET_ACCESS_KEY=your-secret-key" >> .env
echo "AWS_BUCKET=your-bucket-name" >> .env

# Start the application
docker-compose up -d

# View logs
docker-compose logs -f

# Stop the application
docker-compose down
```

### EasyPanel Deployment

For EasyPanel deployment:

1. **Repository**: Connect your Git repository
2. **Build Settings**: 
   - Build Command: `docker build -t whatsapp-api .`
   - Port: `8000`
3. **Environment Variables**: Add your AWS credentials in EasyPanel environment settings
4. **Persistent Storage**: Mount volumes for:
   - `/usr/src/app/sessions` - WhatsApp session data
   - `/usr/src/app/sockets.json` - Socket connection data

The Docker setup includes:
- Node.js 22 runtime optimized for ARM64/AMD64
- All required system dependencies for Puppeteer
- Security hardening with non-root user
- Health checks for container monitoring
- Persistent volume mounting for session data