# DevOps Info Service

## Overview

DevOps Info Service is a simple production-ready Python web application that provides detailed information about the running service, system environment, and runtime state. The service is designed for DevOps practices such as monitoring, health checks, and container readiness probes.

## Prerequisites

* Python 3.10 or higher
* pip (Python package manager)
* Git

## Installation

Clone the repository and navigate to the Python application directory:

```bash
cd app_python
```

Create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

## Running the Application

Run with default configuration:

```bash
python app.py
```

Run with custom configuration using environment variables:

```bash
PORT=8080 python app.py
HOST=127.0.0.1 PORT=3000 python app.py
```

## API Endpoints

### GET /

Returns service metadata, system information, runtime details, request information, and available endpoints.

### GET /health

Health check endpoint used for monitoring and Kubernetes probes. Returns service status, timestamp, and uptime.

## Configuration

The application can be configured using environment variables:

| Variable | Description                     | Default |
| -------- | ------------------------------- | ------- |
| HOST     | Host address to bind the server | 0.0.0.0 |
| PORT     | Port number for the service     | 5000    |
| DEBUG    | Enable debug mode (true/false)  | false   |

## Project Structure

```text
app_python/
├── app.py
├── requirements.txt
├── .gitignore
├── README.md
├── tests/
│   └── __init__.py
└── docs/
    ├── LAB01.md
    └── screenshots/
```

## Notes

* Virtual environment directory (`venv/`) is excluded from version control
* All dependencies are pinned for reproducibility
* The application follows PEP 8 and clean code principles
