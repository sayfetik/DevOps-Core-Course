# Multi-stage build approach for production-ready Python application
# Stage 1: Builder - installs dependencies
FROM python:3.13-slim as builder

# Set working directory
WORKDIR /build

# Copy requirements first (leverages Docker layer caching)
COPY requirements.txt .

# Create a virtual environment in the builder
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Install dependencies in virtual environment
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Runtime - final production image
FROM python:3.13-slim

# Set environment variables
ENV PATH="/opt/venv/bin:$PATH" \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Set working directory
WORKDIR /app

# Create non-root user (security best practice)
RUN useradd --create-home --shell /bin/bash appuser && \
    chown -R appuser:appuser /app

# Copy virtual environment from builder (smaller than pip install in runtime)
COPY --from=builder /opt/venv /opt/venv

# Copy application code
COPY --chown=appuser:appuser app.py .
COPY --chown=appuser:appuser requirements.txt .

# Switch to non-root user
USER appuser

# Expose port
EXPOSE 5000

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD python -c "import socket; socket.create_connection(('localhost', 5000), timeout=2)"

# Default command
CMD ["python", "app.py"]
