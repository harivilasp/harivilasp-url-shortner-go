# URL Shortener — Go + Swift

A full-stack URL shortener with a Go backend and a SwiftUI iOS client.

**Backend:** Go · PostgreSQL · Redis  
**iOS Client:** SwiftUI · CoreData

## Architecture

```
iOS Client (SwiftUI + CoreData)
         │
         │ HTTP
         ▼
  Go API Server (port 9098)
    ├── POST /create-short-url  →  Redis cache + PostgreSQL
    └── GET  /re/:shortUrl      →  Redis lookup → redirect
```

- **Redis** — caches URL mappings for sub-millisecond reads
- **PostgreSQL** — durable store for all shortened URLs
- **CoreData** — client-side history of shortened links

## API

### Shorten a URL

```bash
POST /create-short-url
Content-Type: application/json

{
  "long_url": "https://amazon.com",
  "user_id":  "e0dba740-fc4b-4978-a360-239e"
}
```

**Response (200)**

```json
{
  "message": "short url created successfully",
  "short_url": "http://localhost:9098/re/SwwSgzBe"
}
```

### Redirect

```
GET /re/:shortUrl  →  302 redirect to original URL
```

## Backend Project Structure

```
backend/
├── main.go
├── handler/        # HTTP request handlers
├── shortener/      # URL encoding logic + tests
├── cache/          # Redis client + tests
├── dbservice/      # PostgreSQL CRUD + tests
└── constants/      # Config constants
```

## Getting Started

### Prerequisites

- Go 1.18+
- PostgreSQL
- Redis (`redis-server`)

### Run Backend

```bash
cd backend
redis-server &          # start Redis
go run main.go          # start API on :9098
```

### iOS Client

Open the project in Xcode and run on a simulator or device. The client uses CoreData to persist a local history of shortened links.

## References

- [Building a URL shortener in Go](https://www.eddywm.com/lets-build-a-url-shortener-in-go-part-iv-forwarding/)
