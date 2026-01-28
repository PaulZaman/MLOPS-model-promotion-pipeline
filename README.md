# MLOps Model Promotion Pipeline

A production-grade MLOps pipeline implementing automated model training, quality gates, and multi-stage deployment using MLflow, Docker, and GitHub Actions.

## Overview

This pipeline demonstrates best practices for model lifecycle management with explicit promotion stages (Candidate → Staging → Production) and quality gates to prevent poor models from reaching production.

## Architecture

- **ML Training**: Logistic Regression model with MLflow tracking and registry integration
- **Quality Gates**: Automated accuracy thresholds before staging deployment
- **Staging Environment**: Validates model loading, API serving, and integration
- **Production Environment**: Serves production-grade predictions via Docker

## Components

### ML Pipeline (`ml/`)
- `train_and_register.py`: Trains model and registers to MLflow
- `evaluate.py`: Evaluates model and enforces quality gates
- Model tracking via DagsHub MLflow

### Backend (`backend/`)
- `app.py`: Flask API with `/predict` and `/health` endpoints
- `model_loader.py`: Loads models from MLflow registry by stage
- Dockerized for consistent deployment

### Frontend (`frontend/`)
- `index.html`: Simple UI for model inference testing

### Deployment (`deploy/`)
- `docker-compose.staging.yml`: Staging environment configuration
- `docker-compose.production.yml`: Production environment configuration

## Workflows

### 1. Candidate to Staging
Trains model, logs metrics to MLflow, evaluates against quality gates, and deploys to staging if gates pass.

**Quality Gate**: accuracy >= 0.85

### 2. Promote to Production
Promotes a specific model version from staging to production in the MLflow registry and deploys the production service.

## Setup

### Prerequisites
- Docker and Docker Compose
- Python 3.x
- MLflow server (DagsHub or self-hosted)

### Environment Variables
Create a `.env` file:
```
MLFLOW_TRACKING_URI=<your_mlflow_uri>
MLFLOW_TRACKING_USERNAME=<username>
MLFLOW_TRACKING_PASSWORD=<password>
```

### Running Environments

**Staging**:
```bash
docker compose --env-file .env -f deploy/docker-compose.staging.yml up --build
```

**Production**:
```bash
docker compose --env-file .env -f deploy/docker-compose.production.yml up --build
```

## Key Learnings

### Why Registry Stages Matter
- **Decouples code from models**: Production doesn't deploy "latest code" but rather approved model artifacts
- **Enables rollback**: Any registry version can be promoted/demoted
- **Enforces process**: Models must pass staging before production

### Why Staging is Critical
Beyond offline metrics, staging validates:
- Model loading from registry
- API integration and schema compatibility
- Deployment infrastructure (Docker, dependencies)
- Runtime performance and latency
- Production-like environment behavior

### What Quality Gates Should Include
- **Accuracy**: Statistical model performance
- **Latency**: Real-time inference requirements
- **Schema validation**: Input/output format compatibility
- **Fairness metrics**: Bias detection
- **Robustness tests**: Handling of edge cases and adversarial inputs

## Repository

[GitHub Repository](https://github.com/PaulZamanian/MLOPS-model-promotion-pipeline)