# Simple Python WebSocket Server

A lightweight and simple WebSocket server built with FastAPI, designed to run on [Google Cloud Run](https://cloud.google.com/run?hl=en). This server enables real-time bidirectional communication between clients and the server using WebSocket protocol.

## Features

- WebSocket server implementation using FastAPI
- Broadcasting messages to all connected clients
- JSON message validation
- CORS support for web clients
- Basic error handling and logging
- Ready for Google Cloud Run deployment
- Health check endpoint

## Prerequisites

- Python 3.11 or higher
- Google Cloud SDK
- Docker (for local testing)
- A Google Cloud Project with billing enabled

## Project Structure

```bash
.
├── main.py             # Main application file
├── requirements.txt    # Python dependencies
├── Dockerfile         # Container configuration
└── README.md         # This file
```

## Local Development

1. Create a virtual environment and activate it:

    ```bash
    python -m venv .venv
    source .venv/bin/activate  # On Windows: .venv\Scripts\activate
    ```

2. Install dependencies:

    ```bash
    pip install -r requirements.txt
    ```

3. Run the server locally:

    ```bash
    python main.py
    ```

The server will start at `ws://localhost:8080`.

## Docker Build and Local Testing

1. Build the Docker image:

```bash
docker build -t websocket-server .
```

2.Run the container locally:

```bash
docker run -p 8080:8080 websocket-server
```

## Deployment to Google Cloud Run

1. Set up your Google Cloud project:

    ```bash
    gcloud config set project [YOUR_PROJECT_ID]
    ```

2. Build and push the container:

    ```bash
    gcloud builds submit --tag gcr.io/[YOUR_PROJECT_ID]/websocket-server
    ```

3. Deploy to Cloud Run:

    ```bash
    gcloud run deploy websocket-server \
      --image gcr.io/[YOUR_PROJECT_ID]/websocket-server \
      --platform managed \
      --allow-unauthenticated \
      --port 8080
    ```

## Client Usage

Here's how to connect to the WebSocket server from a web client:

```javascript
const wsUrl = 'wss://your-service-url.run.app/ws';
const ws = new WebSocket(wsUrl);

ws.onopen = () => {
    console.log('Connected to WebSocket server');
    
    // Send a message
    ws.send(JSON.stringify({
        message: "Hello, WebSocket!",
        user: "John"
    }));
};

ws.onmessage = (event) => {
    const data = JSON.parse(event.data);
    console.log('Received:', data);
};
```

## API Endpoints

- `GET /` - Health check endpoint
- `WebSocket /ws` - WebSocket connection endpoint

## Message Format

Messages should be sent as JSON with the following structure:

```json
{
    "message": "Your message content",
    "user": "Username"
}
```

## Limitations and Considerations

1. Cloud Run WebSocket Limitations:
   - Maximum connection duration: 1 hour
   - Automatic connection termination after 1 hour
   - Need for client-side reconnection logic

2. Production Considerations:
   - Implement authentication
   - Configure specific CORS origins
   - Add rate limiting
   - Enhance message validation
   - Set up monitoring and logging
   - Consider using a message queue for scaling

## Error Handling

The server handles several types of errors:
- Invalid JSON format
- Connection errors
- General WebSocket errors

All errors are logged and appropriate error messages are sent back to clients.

## Security Considerations

- In production, update CORS settings to allow only specific origins
- Implement authentication for WebSocket connections
- Regular security audits are recommended
- Keep dependencies updated
