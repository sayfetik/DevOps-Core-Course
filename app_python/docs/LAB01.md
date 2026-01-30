# LAB01 — DevOps Info Service

## Framework Selection

For this lab, **Flask** was chosen as the web framework.

### Why Flask

Flask is a lightweight and minimalistic Python web framework that is easy to understand and quick to set up. It is well suited for small services, microservices, and educational DevOps projects where clarity and simplicity are more important than a large feature set.

Key reasons for choosing Flask:

* Minimal boilerplate and fast development
* Easy request and response handling
* Well-documented and widely used in industry
* Suitable for containerized and cloud-native services

### Framework Comparison

| Framework | Pros                                        | Cons                           |
| --------- | ------------------------------------------- | ------------------------------ |
| Flask     | Lightweight, easy to learn, flexible        | No built-in ORM or auth        |
| FastAPI   | Async, automatic API docs, high performance | Slightly higher learning curve |
| Django    | Full-featured, batteries included           | Heavy for small services       |

Flask was selected as the best balance between simplicity and production readiness for this task.

---

## Best Practices Applied

### 1. Clean Code Organization

The application follows PEP 8 conventions, uses clear function names, and separates concerns logically.

Example:

* `get_system_info()` — collects system-related data
* `get_uptime()` — calculates service uptime

This improves readability, maintainability, and team collaboration.

### 2. Error Handling

Custom error handlers are implemented for common HTTP errors:

* `404 Not Found` — when an endpoint does not exist
* `500 Internal Server Error` — unexpected server errors

This ensures consistent and user-friendly error responses.

### 3. Logging

Logging is configured using Python’s built-in `logging` module.

* Application startup events are logged
* Incoming requests are logged
* Log format includes timestamp and log level

Logging is essential for debugging, monitoring, and production observability.

### 4. Dependency Management

All dependencies are pinned to exact versions in `requirements.txt` to ensure reproducible builds across different environments.

### 5. Configuration via Environment Variables

The service behavior can be customized without changing code using environment variables (`HOST`, `PORT`, `DEBUG`). This follows twelve-factor app principles and is important for containerized deployments.

---

## API Documentation

### GET /

Returns detailed service, system, runtime, and request information.

Example request:

```bash
curl http://127.0.0.1:5000/
```

Example response (shortened):

```json
{
  "service": {
    "name": "devops-info-service",
    "version": "1.0.0",
    "framework": "Flask"
  },
  "system": {
    "hostname": "my-laptop",
    "architecture": "x86_64"
  }
}
```

### GET /health

Health check endpoint for monitoring systems and Kubernetes probes.

Example request:

```bash
curl http://127.0.0.1:5000/health
```

Example response:

```json
{
  "status": "healthy",
  "uptime_seconds": 3600
}
```

---

## Testing Evidence

The following screenshots demonstrate successful execution of the service:

* Main endpoint (`/`) returning full JSON response
* Health check endpoint (`/health`)
* Pretty-printed JSON output in browser or terminal

Screenshots are located in:

```
docs/screenshots/
```

---

## Challenges & Solutions

### Challenge: Python Environment and Dependencies

Initially, the application failed to start due to missing dependencies. This was caused by running the application outside the virtual environment.

**Solution:**
The virtual environment was properly activated and dependencies were installed using `pip install -r requirements.txt`.

---

## GitHub Community

Starring repositories helps open-source maintainers understand which projects are valuable to the community and encourages further development.

Following developers and classmates on GitHub improves collaboration, makes it easier to discover useful projects, and supports professional growth through shared knowledge and visibility.

---

## Conclusion

This lab demonstrates how to build a clean, configurable, and production-ready Python web service following DevOps best practices. The service is suitable for monitoring, containerization, and further automation in future labs.
