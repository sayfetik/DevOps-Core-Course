# LAB01 — Go Implementation

## Implementation Overview

This service mirrors the Python DevOps Info Service functionality using Go's standard `net/http` package.

## Build Process

```bash
go build -o devops-info-service
```

## Binary Size Comparison
Python version: Requires Python runtime and dependencies
Go version: Single compiled binary (~5–8 MB)

## Testing
```bash
curl http://localhost:8080/
curl http://localhost:8080/health
```

## Run
```bash
./devops-info-service
```
Or with custom configuration:

```bash
PORT=9090 ./devops-info-service
```

## API Endpoints

GET / — service and system information

GET /health — health check endpoint
