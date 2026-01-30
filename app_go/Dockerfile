# Multi-stage build for Go application
# Stage 1: Builder
FROM golang:1.22-alpine AS builder
WORKDIR /build
COPY go.mod .
RUN go mod download
COPY main.go .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -ldflags="-w -s" -o devops-info-service .

# Stage 2: Runtime
FROM gcr.io/distroless/static:nonroot
WORKDIR /app
COPY --from=builder /build/devops-info-service .
EXPOSE 8080
USER nonroot
ENTRYPOINT ["/app/devops-info-service"]
