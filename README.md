# Project2 — Flask AWS Monitoring App (DevOps End-to-End Exam)

## Overview

This repository is an end-to-end DevOps project: a containerized Flask application, deployed via a Helm chart to Kubernetes, with a Jenkins pipeline for CI/CD.

Terraform and Azure DevOps are intentionally out of scope for this submission — see **Notes on Scope** below.

## Project Structure

```
proj2/
├── helmchart/
│   ├── templates/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   ├── Chart.yaml
│   ├── values.yaml
│   └── .helmignore
├── .gitignore
├── .dockerignore
├── app.py
├── Dockerfile
├── Jenkinsfile
├── requirements.txt
└── README.md                # this file
```

## What's Implemented

- Flask app (`app.py`) — basic route, runs locally and reads `PORT` from the environment
- `Dockerfile` — builds and runs successfully; tested locally with `docker build` / `docker run`
- Helm chart — `Chart.yaml`, `values.yaml`, and templates for a Kubernetes `Deployment` and `Service`
- `.gitignore` / `.dockerignore` / `.helmignore` in place

## What's Missing / In Progress

- AWS monitoring functionality (EC2 instances, VPCs, Load Balancers, AMIs) — not yet implemented; will be mocked with static data rather than live AWS calls, since AWS/boto3 hasn't been covered in coursework yet
- `Jenkinsfile` — currently empty; needs linting and security scans running in parallel, then Docker build/push to Docker Hub
- Helm `Ingress` manifest — not yet written
- Git Flow branching (`dev` branch, feature branches, PR-only merges into `main`) — not yet set up
- Terraform — out of scope, not covered in coursework
- Azure DevOps pipeline — out of scope (bonus), not covered in coursework

## How to Run

### Locally

```bash
pip install -r requirements.txt
python app.py
```

Visit `http://localhost:8080`.

### With Docker

```bash
docker build -t project2-app .
docker run -p 8080:8080 project2-app
```

### With Helm

```bash
helm lint ./helmchart
helm template ./helmchart
helm install project2 ./helmchart
```

## Notes on Scope

This project is being built as part of a DevOps course where Terraform and Azure DevOps haven't been covered yet, so both are intentionally excluded from this submission rather than attempted incompletely. The AWS-monitoring functionality will similarly be implemented with mocked/static data instead of live `boto3` calls, since that integration hasn't been covered in coursework either. Mocked sections will be clearly marked with comments in the relevant source files once added.
