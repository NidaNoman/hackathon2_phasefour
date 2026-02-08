# AI Todo Chatbot Application

A full-stack AI-powered Todo application with chatbot functionality, built with Next.js, FastAPI, and Google Gemini.

## Project Phases

- **Phase 1**: CLI Todo Application
- **Phase 2**: Web Application (Frontend + Backend)
- **Phase 3**: AI Chatbot Integration
- **Phase 4**: Local Kubernetes Deployment (Current)

---

## Phase 4: Local Kubernetes Deployment

This phase focuses on containerizing the application and deploying it to a local Kubernetes cluster using Minikube.

### Prerequisites

Before starting, ensure you have the following installed:

- **Docker Desktop** (with Docker Engine running)
- **Minikube** v1.30+
- **kubectl** CLI
- **Helm** v3.12+
- At least **4GB RAM** available for Minikube

### Quick Start

#### Option 1: Automated Deployment (Recommended)

**Windows PowerShell:**
```powershell
# Set environment variables
$env:DATABASE_URL = "your_database_connection_string"
$env:SECRET_KEY = "your_jwt_secret_key"
$env:GEMINI_API_KEY = "your_gemini_api_key"
$env:COHERE_API_KEY = "your_cohere_api_key"

# Run deployment script
.\scripts\deploy-minikube.ps1
```

**Linux/macOS:**
```bash
# Set environment variables
export DATABASE_URL="your_database_connection_string"
export SECRET_KEY="your_jwt_secret_key"
export GEMINI_API_KEY="your_gemini_api_key"
export COHERE_API_KEY="your_cohere_api_key"

# Run deployment script
chmod +x ./scripts/deploy-minikube.sh
./scripts/deploy-minikube.sh
```

#### Option 2: Manual Deployment

##### Step 1: Start Minikube

```bash
# Start Minikube with sufficient resources
minikube start --cpus=2 --memory=4096 --driver=docker
```

##### Step 2: Configure Docker to Use Minikube's Daemon

**Linux/macOS:**
```bash
eval $(minikube docker-env)
```

**Windows PowerShell:**
```powershell
& minikube -p minikube docker-env --shell powershell | Invoke-Expression
```

##### Step 3: Build Docker Images

```bash
# Build frontend image
docker build -t todo-frontend:latest -f docker/Dockerfile.frontend ./frontend

# Build backend image
docker build -t todo-backend:latest -f docker/Dockerfile.backend ./backend
```

##### Step 4: Deploy with Helm

```bash
# Install the Helm chart with secrets
helm upgrade --install todo-app ./helm/todo-app \
  --set secrets.databaseUrl="YOUR_DATABASE_URL" \
  --set secrets.secretKey="YOUR_SECRET_KEY" \
  --set secrets.geminiApiKey="YOUR_GEMINI_API_KEY" \
  --set secrets.cohereApiKey="YOUR_COHERE_API_KEY"
```

##### Step 5: Access the Application

```bash
# Get the frontend URL
minikube service todo-frontend --url
```

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Minikube Cluster                          │
│                                                                  │
│  ┌────────────────────┐      ┌────────────────────┐            │
│  │   Frontend Pod     │      │   Backend Pod      │            │
│  │   (Next.js)        │──────│   (FastAPI)        │            │
│  │   Port: 3000       │      │   Port: 8000       │            │
│  └────────────────────┘      └────────────────────┘            │
│           │                            │                        │
│  ┌────────────────────┐      ┌────────────────────┐            │
│  │  Frontend Service  │      │  Backend Service   │            │
│  │  (NodePort: 30080) │      │  (NodePort: 30081) │            │
│  └────────────────────┘      └────────────────────┘            │
│                                        │                        │
└─────────────────────────────────────────────────────────────────┘
                                         │
                              ┌──────────────────────┐
                              │  External Services   │
                              │  - Neon PostgreSQL   │
                              │  - Google Gemini API │
                              └──────────────────────┘
```

### Useful Commands

```bash
# Check pod status
kubectl get pods

# View pod logs
kubectl logs -f -l app.kubernetes.io/component=frontend
kubectl logs -f -l app.kubernetes.io/component=backend

# Check services
kubectl get svc

# Scale deployments
kubectl scale deployment todo-frontend --replicas=3
kubectl scale deployment todo-backend --replicas=2

# Upgrade deployment with new values
helm upgrade todo-app ./helm/todo-app --set frontend.replicaCount=2

# Uninstall the application
helm uninstall todo-app
```

### Troubleshooting

#### Pods stuck in ImagePullBackOff
- Ensure you're using Minikube's Docker daemon
- Re-run the docker-env command and rebuild images

```bash
eval $(minikube docker-env)  # Linux/Mac
docker build -t todo-frontend:latest -f docker/Dockerfile.frontend ./frontend
docker build -t todo-backend:latest -f docker/Dockerfile.backend ./backend
```

#### Frontend cannot connect to backend
- Verify the backend service is running: `kubectl get svc todo-backend`
- Check backend logs: `kubectl logs -l app.kubernetes.io/component=backend`

#### Database connection errors
- Verify DATABASE_URL is correctly set in secrets
- Ensure Neon PostgreSQL allows connections from Minikube's IP

#### Pods in CrashLoopBackOff
- Check pod logs for error details: `kubectl logs <pod-name>`
- Describe the pod for events: `kubectl describe pod <pod-name>`

### Cleanup

```bash
# Remove Helm release
helm uninstall todo-app

# Stop Minikube
minikube stop

# Delete Minikube cluster (optional)
minikube delete
```

---

## Project Structure

```
.
├── backend/                 # FastAPI backend application
│   ├── app/
│   │   ├── api/            # API routes
│   │   ├── core/           # Core configurations
│   │   ├── crud/           # Database operations
│   │   ├── db/             # Database models
│   │   └── schemas/        # Pydantic schemas
│   └── requirements.txt
├── frontend/               # Next.js frontend application
│   ├── src/
│   │   ├── app/           # App router pages
│   │   ├── components/    # React components
│   │   └── lib/           # Utilities and state
│   └── package.json
├── docker/                 # Docker configurations
│   ├── Dockerfile.frontend
│   └── Dockerfile.backend
├── helm/                   # Helm chart for Kubernetes
│   └── todo-app/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
├── scripts/                # Deployment scripts
│   ├── deploy-minikube.sh
│   └── deploy-minikube.ps1
└── specs/                  # Project specifications
    └── deployment/
        └── phase4-local-k8s.md
```

## License

MIT License
"# hackathon2_phasefour" 
