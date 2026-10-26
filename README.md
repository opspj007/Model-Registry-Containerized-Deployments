<div align="center">
<img width="1200" height="475" alt="GHBanner" src="https://ai.google.dev/static/site-assets/images/share-ais-513315318.png" />
</div>

# Run and deploy your AI Studio app

This contains everything you need to run your app locally.

View your app in AI Studio: https://ai.studio/apps/8777e2bb-bd54-4d0e-a7c6-20cf0ce8ca7c

## Run Locally

**Prerequisites:**  Node.js


1. Install dependencies:
   `npm install`
2. Set the `GEMINI_API_KEY` in [.env.local](.env.local) to your Gemini API key
3. Run the app:
   `npm run dev`


# MLServe — Model Registry & Containerized ML Deployment Platform

**Build • Register • Deploy • Benchmark • Monitor**

MLServe is a machine learning infrastructure platform designed to simplify model registration, version management, containerized deployment, inference serving, and performance monitoring through a unified API.

The project demonstrates practical software engineering concepts relevant to production ML systems, including REST API design, model lifecycle management, Docker-based deployment, automated testing, inference benchmarking, and observability.

> **Project status:** MVP / Active Development  
> **Primary language:** Python  
> **Backend:** FastAPI  
> **Containerization:** Docker  
> **Machine Learning:** scikit-learn  
> **Monitoring:** Prometheus-compatible metrics

---

## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Project Goals](#project-goals)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Running with Docker](#running-with-docker)
- [API Documentation](#api-documentation)
- [Example API Requests](#example-api-requests)
- [Model Lifecycle](#model-lifecycle)
- [Performance Benchmarking](#performance-benchmarking)
- [Monitoring and Observability](#monitoring-and-observability)
- [Testing](#testing)
- [Configuration](#configuration)
- [Security Considerations](#security-considerations)
- [Limitations](#limitations)
- [Future Enhancements](#future-enhancements)
- [Learning Outcomes](#learning-outcomes)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

Machine learning models are useful only when they can be deployed reliably and served efficiently. Managing multiple model versions, maintaining reproducible environments, measuring inference performance, and identifying failures can become challenging as ML applications grow.

MLServe addresses these challenges through a lightweight model-serving and deployment platform.

It provides a backend for registering model metadata, serving predictions, evaluating model performance, benchmarking inference requests, and exposing operational metrics. Docker provides a reproducible environment for running the service.

The initial implementation uses a scikit-learn classification model as a reference workload. The architecture can later be extended to support larger models, additional model frameworks, centralized artifact storage, and orchestrated deployments.

### Project highlights

- Centralized model metadata and version tracking.
- RESTful inference endpoints built with FastAPI.
- Containerized application deployment with Docker.
- Model evaluation and prediction workflows.
- Inference benchmarking and operational metrics.
- Automated tests for important application behavior.
- Extensible architecture for future ML infrastructure capabilities.

---

## Problem Statement

ML applications often encounter several operational challenges:

1. **Model version management:** Teams need to identify which model version is registered and being served.
2. **Deployment consistency:** Models may behave differently when development and production environments differ.
3. **Inference performance:** High latency or low throughput can negatively affect application responsiveness.
4. **Operational visibility:** Teams need metrics and error information to understand service behavior.
5. **Reproducibility:** Model artifacts, dependencies, and configuration should be managed consistently.
6. **Release safety:** Replacing a model should not unnecessarily interrupt inference services.

MLServe provides an MVP foundation for exploring these challenges through an implementable, testable software system.

---

## Project Goals

The project is designed to:

- Build a maintainable API for ML inference.
- Introduce model registration and version-aware metadata.
- Package the application into a Docker container.
- Measure inference latency and request throughput.
- Expose metrics for monitoring and debugging.
- Establish automated testing practices.
- Create a foundation for future scalable deployment strategies.

---

## Key Features

### 1. Model Registry

The registry maintains information about available models and their metadata.

Expected registry information may include:

- Model name and version.
- Model artifact location.
- Model framework.
- Registration timestamp.
- Evaluation metrics.
- Deployment status.

The initial MVP uses lightweight local metadata storage. A production implementation should use a durable database and a dedicated artifact store.

### 2. Model Inference API

The FastAPI backend exposes HTTP endpoints for model predictions.

Core responsibilities include:

- Accepting structured input data.
- Validating request payloads.
- Loading the relevant model.
- Generating predictions.
- Returning structured JSON responses.
- Reporting validation and runtime errors.

### 3. Model Evaluation

Evaluation endpoints support the measurement of model quality using an appropriate dataset and metrics.

For classification workloads, possible metrics include:

- Accuracy.
- Precision.
- Recall.
- F1 score.

The appropriate metrics depend on the model and use case.

### 4. Containerized Deployment

Docker packages the API and its runtime dependencies into a reproducible application image.

Benefits include:

- Consistent execution environments.
- Simplified setup.
- Easier testing and deployment.
- Isolation of application dependencies.
- A foundation for container orchestration.

### 5. Performance Benchmarking

The platform can benchmark inference behavior by sending repeated prediction requests and collecting timing information.

Useful metrics include:

- Average latency.
- Median latency.
- p95 and p99 latency.
- Requests per second.
- Error rate.
- CPU and memory usage.

Benchmark results should be interpreted in the context of hardware, concurrency, payload size, and test duration.

### 6. Monitoring and Observability

Prometheus-compatible metrics can expose operational information about the API.

Monitoring can help identify:

- Increased request latency.
- Elevated error rates.
- Changes in request volume.
- Resource constraints.
- Performance regressions.

Metrics availability depends on the endpoints and instrumentation implemented in the current version.

---

## System Architecture

```text
                 ┌─────────────────────────┐
                 │  Developer / API Client │
                 └────────────┬────────────┘
                              │ HTTP / JSON
                              ▼
                 ┌─────────────────────────┐
                 │      FastAPI API        │
                 │                         │
                 │  Request Validation     │
                 │  Inference Endpoints    │
                 │  Evaluation Endpoints   │
                 │  Benchmark Endpoints    │
                 └────────────┬────────────┘
                              │
                 ┌────────────┴────────────┐
                 ▼                         ▼
       ┌──────────────────┐     ┌──────────────────┐
       │  Model Registry  │     │  Model Inference │
       │                  │     │                  │
       │ Metadata         │     │ scikit-learn     │
       │ Versions         │     │ Model Artifacts  │
       └──────────────────┘     └──────────────────┘
                 │                         │
                 └────────────┬────────────┘
                              ▼
                 ┌─────────────────────────┐
                 │ Metrics and Benchmarking│
                 │                         │
                 │ Latency                 │
                 │ Throughput              │
                 │ Errors                  │
                 └────────────┬────────────┘
                              ▼
                 ┌─────────────────────────┐
                 │ Dockerized Application  │
                 └─────────────────────────┘
```

### Architecture notes

- FastAPI provides the HTTP interface.
- The registry tracks model metadata and versions.
- The inference layer executes predictions.
- Benchmarking measures service performance.
- Docker provides a consistent runtime environment.
- Prometheus-compatible instrumentation supports observability.

This diagram represents the logical architecture. The implementation may differ in the details of model storage, loading, and metrics collection.

---

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Application and ML development |
| FastAPI | REST API framework |
| Pydantic | Request and response validation |
| scikit-learn | Reference machine learning model |
| NumPy | Numerical operations |
| Docker | Containerization |
| Docker Compose | Local multi-service orchestration |
| Prometheus client | Metrics instrumentation |
| Pytest | Automated testing |
| HTTPX | API testing |
| Git and GitHub | Version control and collaboration |

**Potential extensions:** MLflow for lifecycle management, PostgreSQL for durable metadata, object storage for model artifacts, Kubernetes for orchestration, and Grafana for dashboards.

These extensions should be described as planned until they have been implemented and tested.

---

## Project Structure

The repository is organized around the API, model-serving workflow, deployment configuration, and tests.

```text
mlserve-platform/
├── app/
│   ├── main.py
│   ├── models/
│   ├── services/
│   └── registry/
├── tests/
│   ├── test_api.py
│   └── test_models.py
├── artifacts/
│   └── model files
├── requirements.txt
├── Dockerfile
├── docker-compose.yml
├── .dockerignore
├── .gitignore
├── .env.example
└── README.md
```

The exact paths depend on the current repository version. Keep this section synchronized with the files actually committed to GitHub.

---

## Prerequisites

Install the following software before running the project:

- Python 3.11 or another version supported by the dependency configuration.
- pip.
- Git.
- Docker Desktop or Docker Engine.
- Docker Compose, if using the Compose configuration.

Verify the installations:

```bash
python --version
pip --version
git --version
docker --version
docker compose version
```

---

## Installation

### 1. Clone the repository

Replace the placeholder with your actual GitHub repository URL.

```bash
git clone <YOUR_REPOSITORY_URL>
cd mlserve-platform
```

### 2. Create a virtual environment

**Windows PowerShell:**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

**Linux or macOS:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Start the application

Use the import path that matches your repository. For example, if the FastAPI instance is defined as `app` inside `app/main.py`:

```bash
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

If your project uses a different module path, adjust the command accordingly.

### 5. Open the API documentation

Once the service starts, open:

- Swagger UI: `http://127.0.0.1:8000/docs`
- ReDoc: `http://127.0.0.1:8000/redoc`
- OpenAPI schema: `http://127.0.0.1:8000/openapi.json`

The documentation lists the routes actually registered by the running application.

---

## Running with Docker

Docker is the recommended way to demonstrate a consistent application environment.

### Build the image

```bash
docker build -t mlserve:local .
```

### Run the container

```bash
docker run --rm -p 8000:8000 mlserve:local
```

Open `http://localhost:8000/docs` to inspect the API.

### Using Docker Compose

If the repository includes a valid Compose configuration:

```bash
docker compose up --build
```

Run the services in the background:

```bash
docker compose up --build -d
```

Inspect service status:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f
```

Stop the services:

```bash
docker compose down
```

**Important:** The container's startup command, internal port, persistent storage configuration, and health checks must match the actual Dockerfile and Compose file.

---

## API Documentation

The following table describes typical endpoint categories. Confirm the exact paths and HTTP methods against the running application's OpenAPI documentation.

| Endpoint category | Purpose |
|---|---|
| Health check | Verify service availability |
| Model registry | Inspect registered model metadata |
| Prediction | Generate model predictions |
| Evaluation | Measure model quality |
| Benchmark | Measure inference performance |
| Metrics | Expose operational metrics |

Example route names might include `/health`, `/models`, `/predict`, `/evaluate`, `/benchmark`, and `/metrics`. These are illustrative names, not a guarantee that every route exists in the current build.

### Example prediction request

A classification model might accept a request such as:

```json
{
  "features": [5.1, 3.5, 1.4, 0.2]
}
```

A successful response might contain:

```json
{
  "prediction": 0,
  "model_version": "1.0"
}
```

The exact request and response formats depend on the API's Pydantic schemas and model implementation.

---

## Example API Requests

If the application exposes the corresponding endpoints, these examples can be used to test it.

### Health check

```bash
curl http://localhost:8000/health
```

### Inspect models

```bash
curl http://localhost:8000/models
```

### Generate a prediction

```bash
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"features":[5.1,3.5,1.4,0.2]}'
```

### Inspect metrics

```bash
curl http://localhost:8000/metrics
```

If your operating system or API schema differs, adjust the requests to match the actual implementation.

---

## Model Lifecycle

A model lifecycle workflow typically includes the following stages:

### Stage 1: Training

Train a model using a selected dataset and record the training configuration.

### Stage 2: Evaluation

Evaluate the trained model against appropriate validation data. Record relevant quality metrics.

### Stage 3: Registration

Register the model with a unique name and version. Associate it with its artifact and evaluation metadata.

### Stage 4: Deployment

Package the model-serving application into a Docker image and start the inference service.

### Stage 5: Inference

Accept prediction requests and return results through the REST API.

### Stage 6: Monitoring

Track latency, throughput, errors, and resource consumption.

### Stage 7: Version update or rollback

When a new model version is available, evaluate it before switching production traffic. Retain a safe way to restore the previous version.

Automated promotion, canary deployment, and rollback require additional implementation beyond a basic registry and prediction API.

---

## Performance Benchmarking

Performance engineering is an important part of this project.

The goal is to evaluate not only model accuracy but also the behavior of the serving system under load.

### Recommended metrics

| Metric | Meaning |
|---|---|
| Average latency | Mean time taken per request |
| p50 latency | Median request latency |
| p95 latency | Latency at the 95th percentile |
| p99 latency | Latency at the 99th percentile |
| Throughput | Successfully processed requests per second |
| Error rate | Fraction of requests that fail |
| CPU usage | Processor utilization |
| Memory usage | Memory consumed by the process or container |

### Suggested experiment

1. Warm up the application before collecting measurements.
2. Run a baseline with sequential requests.
3. Repeat the experiment with controlled concurrent clients.
4. Record latency percentiles, successful requests, and errors.
5. Repeat each test several times.
6. Compare results under the same hardware and workload conditions.
7. Document the test environment and configuration.

### Results table

Populate this table only after running the benchmarks.

| Test configuration | Requests | Concurrency | p95 latency | Throughput | Error rate |
|---|---:|---:|---:|---:|---:|
| Baseline | TBD | 1 | TBD | TBD | TBD |
| Moderate load | TBD | TBD | TBD | TBD | TBD |
| Higher load | TBD | TBD | TBD | TBD | TBD |

Do not report invented latency improvements or throughput numbers.

---

## Monitoring and Observability

A reliable ML service needs more than prediction endpoints.

Monitoring should help developers determine whether a problem comes from the API, model loading, input validation, or the runtime environment.

Recommended practices:

- Record request counts and response status codes.
- Measure inference latency.
- Track application exceptions.
- Monitor CPU and memory consumption.
- Expose metrics in a format compatible with Prometheus.
- Use structured logs with request identifiers.
- Avoid logging sensitive request payloads or credentials.

A future deployment can connect Prometheus to the service and Grafana to visualize latency, traffic, and error trends.

---

## Testing

Automated tests help protect the application as model and API functionality evolves.

### Run the test suite

```bash
pytest -v
```

### Recommended test coverage

- Health endpoint returns the expected status.
- Valid prediction requests return the expected response structure.
- Invalid feature dimensions are rejected.
- Unknown model names or versions produce appropriate errors.
- Model registration handles duplicate or invalid entries safely.
- Benchmark endpoints handle invalid parameters.
- Metrics are exposed when instrumentation is enabled.
- The application starts correctly inside its Docker container.

### Testing strategy

Use unit tests for individual functions, API tests for HTTP behavior, and integration tests for workflows involving model artifacts and registry storage.

Record actual test results in the repository or CI logs rather than claiming a particular coverage percentage without measuring it.

---

## Configuration

Use environment variables for configuration that differs between development and deployment environments.

Potential settings include:

```text
APP_ENV=development
APP_HOST=0.0.0.0
APP_PORT=8000
MODEL_REGISTRY_PATH=./registry
MODEL_ARTIFACT_PATH=./artifacts
LOG_LEVEL=INFO
```

These are example configuration names. Define and document them in the code before relying on them.

For local development, a `.env.example` file can document safe defaults. Do not commit real secrets, access tokens, or production credentials.

---

## Security Considerations

Before exposing the service beyond a trusted local environment, consider the following:

- Add authentication and authorization.
- Validate all request payloads and impose size limits.
- Restrict model artifact formats to trusted sources.
- Avoid loading untrusted serialized Python objects.
- Run containers as a non-root user where practical.
- Keep dependencies updated.
- Use HTTPS behind an appropriately configured gateway or reverse proxy.
- Add request timeouts and rate limiting.
- Protect metrics endpoints if they reveal internal operational information.
- Restrict writable directories and apply appropriate filesystem permissions.

A development MVP should not be assumed to be production-secure without a security review.

---

## Limitations

The initial version is intended as a learning and demonstration project.

Potential limitations include:

- Local metadata storage may not support reliable multi-instance coordination.
- A lightweight registry may not provide full artifact lineage or governance.
- A single inference service does not automatically provide high availability.
- Local benchmarking does not guarantee production performance.
- Authentication, distributed tracing, and advanced deployment strategies may require additional development.
- A sample classification model does not establish performance for large language models or GPU-intensive workloads.

These limitations define clear opportunities for future engineering work.

---

## Future Enhancements

### Phase 1: Stronger registry

- Integrate MLflow.
- Store metadata in PostgreSQL.
- Add artifact checksums and lineage.
- Track model parameters and evaluation metrics.

### Phase 2: Production-grade inference

- Add configurable worker processes.
- Implement request timeouts and structured error handling.
- Add readiness and liveness probes.
- Add persistent storage for artifacts.
- Separate model loading from request handling where appropriate.

### Phase 3: Performance engineering

- Add reproducible load-testing scripts.
- Profile CPU, memory, and inference execution.
- Test batching and concurrency strategies.
- Track p95 and p99 latency.
- Compare model versions under identical workloads.

### Phase 4: Orchestration

- Deploy to Kubernetes.
- Configure resource requests and limits.
- Add rolling deployments.
- Introduce canary releases and rollback workflows.
- Add horizontal scaling where appropriate.

### Phase 5: Advanced ML infrastructure

- Support multiple model frameworks.
- Add GPU-aware inference.
- Introduce model caching and dynamic loading.
- Explore asynchronous or distributed inference.
- Implement model drift monitoring and scheduled evaluation.

---

## Learning Outcomes

This project offers practical experience in:

- Designing Python backend services.
- Building REST APIs for ML inference.
- Managing model metadata and versions.
- Containerizing ML applications.
- Writing automated tests.
- Measuring latency and throughput.
- Instrumenting APIs for monitoring.
- Reasoning about reliability and deployment trade-offs.
- Identifying bottlenecks through controlled performance experiments.

These skills are relevant to backend engineering, ML platform engineering, and AI infrastructure development.

---

## Contributing

Contributions are welcome.

1. Fork the repository.
2. Create a feature branch.
3. Implement your changes with appropriate tests.
4. Run the test suite.
5. Submit a pull request describing the change and its impact.

Suggested contribution areas include registry persistence, additional model formats, performance testing, API validation, monitoring, and deployment automation.

---

## License

Choose a license appropriate for your project before publishing it publicly. For example, an MIT License permits broad reuse subject to its terms.

If you select MIT, add a `LICENSE` file containing the full MIT License text and your copyright information.

---

## Author

**Om Prakash Chaudhary**

Full Stack Developer | Python AI Engineer | Generative AI Engineer

- GitHub: https://github.com/opspj007
- LinkedIn: https://www.linkedin.com/in/om-prakash-chaudhary-nims/

---

## Final Note

MLServe is designed as a foundation for exploring the operational side of machine learning: model management, reproducible deployment, inference performance, and service observability.

The long-term goal is to evolve it from a local MVP into a robust ML serving platform with persistent model tracking, measured performance, automated deployment, and reliable rollback capabilities.

**Build it. Measure it. Improve it. Deploy it reliably.**
