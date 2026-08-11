# BBC News Site

A front-end clone of the BBC News homepage built with **React** and **Vite**, paired with a complete DevOps pipeline for containerized deployment — Docker, Jenkins CI/CD, Kubernetes, and Terraform-provisioned AWS infrastructure.

## Overview

This project has two parts:

1. **`bbc-news/`** — the React application: a static recreation of the BBC News homepage layout (navigation bar and a feed of news cards) styled with Bootstrap.
2. **Infrastructure & deployment** — a `Dockerfile`, `Jenkinsfile`, Kubernetes `service.yaml`, and Terraform configs (`terraform/`) that build, containerize, and ship the app to AWS.

## Tech Stack

**Frontend**
- [React 19](https://react.dev/)
- [Vite 7](https://vitejs.dev/) — dev server and build tool
- [React Bootstrap](https://react-bootstrap.github.io/) + [Bootstrap 5](https://getbootstrap.com/)
- [React Router](https://reactrouter.com/)
- ESLint for linting

**DevOps / Infrastructure**
- **Docker** — multi-stage build (Node.js build stage → Nginx runtime)
- **Jenkins** — CI/CD pipeline (`Jenkinsfile`) that builds the image, runs it, and pushes it to Docker Hub
- **Kubernetes** — deployment via `service.yaml` (namespace, pod, and `LoadBalancer` service)
- **Terraform** — provisions AWS infrastructure (VPC, subnets, internet gateway, route tables, security groups, and EC2 instances for Jenkins, Docker, and EKS) in the `ap-southeast-1` region

## Project Structure

```
BBC_News_Site/
├── Dockerfile              # Multi-stage build: Node build -> Nginx serve
├── Jenkinsfile             # CI/CD pipeline definition
├── service.yaml            # Kubernetes namespace, pod, and service
├── terraform/              # AWS infrastructure as code
│   ├── provider.tf
│   ├── vpc.tf
│   ├── subnets.tf
│   ├── igw.tf
│   ├── route_table.tf
│   ├── security_group.tf
│   └── ec2.tf
└── bbc-news/                # React application
    ├── src/
    │   ├── App.jsx
    │   ├── Navigate.jsx     # Top navigation bar
    │   ├── Index.jsx        # News feed / homepage content
    │   ├── main.jsx
    │   └── assets/
    ├── public/
    ├── index.html
    ├── package.json
    └── vite.config.js
```

## Getting Started (Local Development)

### Prerequisites
- [Node.js](https://nodejs.org/) (v20 or later recommended)
- npm

### Setup

```bash
# Clone the repository
git clone https://github.com/harikrishnan-knr/BBC_News_Site.git
cd BBC_News_Site/bbc-news

# Install dependencies
npm install

# Start the dev server
npm run dev
```

The app will be available at `http://localhost:5173` (Vite's default port).

### Available Scripts

Run these from inside the `bbc-news/` directory:

| Script | Description |
|---|---|
| `npm run dev` | Start the Vite development server |
| `npm run build` | Build the app for production |
| `npm run preview` | Preview the production build locally |
| `npm run lint` | Run ESLint |

## Running with Docker

Build and run the app in a container (served via Nginx on port 80):

```bash
docker build -t bbcnews:latest .
docker run -d --name bbcsite -p 80:80 bbcnews:latest
```

Visit `http://localhost` to view the app.

## Deployment

This project is set up for a full CI/CD workflow:

1. **Terraform** provisions the underlying AWS infrastructure (VPC, subnets, security groups, and EC2 instances for Jenkins, Docker, and EKS).
2. **Jenkins** (`Jenkinsfile`) checks out the code, builds the Docker image, runs/tests it locally, pushes it to Docker Hub, then deploys it to a Kubernetes cluster.
3. **Kubernetes** (`service.yaml`) creates a dedicated namespace and exposes the app publicly via a `LoadBalancer` service on port 80.

> **Note:** The Terraform and Jenkins configs reference specific AWS resources (AMI IDs, key pairs, Docker Hub credentials, etc.) that you'll need to adjust for your own environment before deploying.

## License

No license file is currently included in this repository. Add one if you intend to open-source this project.
