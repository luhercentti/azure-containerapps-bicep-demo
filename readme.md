<h2><strong>Luis Angelo Hernandez Centti</strong></h2>

---

# Azure Full Stack Task App — Demo

A full-stack task management application demonstrating how to deploy a containerized Python API and a static React frontend to Microsoft Azure, with infrastructure fully provisioned via Terraform.

---

## Overview

| Component | Technology | Azure Hosting |
|-----------|-----------|---------------|
| Backend API | Python / FastAPI | Azure Container Apps |
| Frontend UI | React 18 (CRA) | Azure Static Web Apps |
| Infrastructure | Terraform (AzureRM ~3.0) | East US |

---

## Architecture

```
User
 │
 ├──▶ Azure Static Web App (React)
 │         │
 │         └──▶ Azure Container App (FastAPI — port 8000)
 │                   │
 │                   └──▶ Azure Container Registry (Docker image)
 │
 └── Monitoring: Azure Log Analytics Workspace
```

All Azure resources live in a single resource group and are managed by Terraform.

---

## Backend (`/backend`)

A REST API built with **FastAPI**, containerized with Docker, and served by **Uvicorn** on port `8000`.

### Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/` | Root health message |
| `GET` | `/health` | Health check |
| `GET` | `/api/tasks` | List all tasks |
| `POST` | `/api/tasks` | Create a new task |
| `PUT` | `/api/tasks/{id}` | Update a task (title / completed) |
| `DELETE` | `/api/tasks/{id}` | Delete a task |

### Run locally

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload --port 8000
```

### Run tests

```bash
cd backend
pytest tests/
```

### Build & run with Docker

```bash
docker build -t taskapp-backend ./backend
docker run -p 8000:8000 taskapp-backend
```

---

## Frontend (`/frontend`)

A single-page application built with **React 18**. It talks to the backend via the `REACT_APP_API_URL` environment variable (defaults to `http://localhost:8000`).

### Features

- View all tasks
- Add a new task
- Toggle task completion
- Delete a task

### Run locally

```bash
cd frontend
npm install
npm start
```

### Build for production

```bash
npm run build
```

---

## Infrastructure (`/terraform`)

Terraform configuration that provisions all required Azure resources:

| Resource | Details |
|----------|---------|
| Resource Group | `luis.angelo` — East US |
| Azure Container Registry | `acrlhc`, Basic SKU — stores the backend Docker image |
| Log Analytics Workspace | 30-day retention for container logs |
| Container Apps Environment | Managed runtime for the backend container |
| Azure Container App | FastAPI API; 0.25 vCPU / 0.5 GiB RAM; 1–3 replicas; HTTPS external ingress |
| Azure Static Web App | React frontend; Free tier (East US 2); auto-configured with the API URL |

### Deploy

```bash
cd terraform
terraform init
terraform plan
terraform apply
```

### Outputs

| Output | Description |
|--------|-------------|
| `container_app_url` | Public HTTPS URL of the FastAPI backend |
| `static_web_app_url` | Public URL of the React frontend |
| `acr_login_server` | ACR login server (for pushing Docker images) |
| `resource_group_name` | Azure resource group name |

---

## Project Structure

```
.
├── backend/
│   ├── main.py            # FastAPI application & routes
│   ├── requirements.txt   # Python dependencies
│   ├── Dockerfile         # Container image definition
│   └── tests/
│       └── test_main.py   # Pytest API tests
├── frontend/
│   ├── package.json
│   ├── public/
│   │   └── index.html
│   └── src/
│       ├── App.js         # Main React component (task UI)
│       ├── App.test.js
│       └── index.js
├── terraform/
│   ├── main.tf            # Azure resource definitions
│   ├── variables.tf       # Input variables (project name, ACR name)
│   └── outputs.tf         # Output values
└── readme.md
```

---

## Key Dependencies

**Backend**
- `fastapi==0.110.0`
- `uvicorn[standard]==0.27.0`
- `pytest==8.1.1`
- `httpx==0.27.0`

**Frontend**
- `react ^18.2.0`
- `react-dom ^18.2.0`
- `react-scripts 5.0.1`
