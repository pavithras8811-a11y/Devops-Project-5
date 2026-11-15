# Project 3: Kubernetes Application Deployment and Rolling Updates

## 1. Abstract
This project deploys a small Node.js web application to a local Kubernetes cluster. It uses a Kubernetes Deployment to maintain two application replicas and a Service to expose the application. Readiness and liveness probes check application health, while rolling updates and rollbacks demonstrate safer application release practices.

## 2. Introduction
Modern applications are commonly packaged as containers and managed by container orchestration platforms. Kubernetes automates deployment, scaling, service discovery, and recovery of containerized applications. This project provides a local learning environment for core Kubernetes concepts.

## 3. Problem Statement
Running containers manually can become difficult when an application needs multiple replicas, health checks, controlled updates, and recovery from an unsuccessful release. Kubernetes provides resources to manage these tasks declaratively.

## 4. Objectives
- Build a Docker image for a Node.js web application.
- Run a local Kubernetes cluster using Minikube.
- Deploy the application with two replicas.
- Expose the application through a Kubernetes Service.
- Configure readiness and liveness probes.
- Perform a rolling update.
- Demonstrate rollback to an earlier revision.

## 5. Scope
The project is designed for a local learning cluster. It demonstrates basic deployment and update workflows; it does not include production ingress, TLS, autoscaling, persistent storage, secrets management, or a multi-node production cluster.

## 6. Technologies Used
| Technology | Purpose |
|---|---|
| Node.js | Runs the sample web application |
| Docker | Builds the application container image |
| Kubernetes | Orchestrates application containers |
| Minikube | Provides a local Kubernetes cluster |
| kubectl | Applies and inspects Kubernetes resources |

## 7. Prerequisites
- Docker installed and running.
- Minikube installed.
- kubectl installed.
- Basic terminal knowledge.
- Sufficient system resources for a local Kubernetes cluster.

## 8. System Architecture
1. The Node.js application listens on port 3000.
2. Docker packages the app into an image.
3. Minikube provides the local Kubernetes cluster.
4. A Deployment maintains two Pods using the application image.
5. Readiness and liveness probes call the `/health` endpoint.
6. A NodePort Service exposes port 80 and forwards traffic to container port 3000.
7. Kubernetes updates Pods gradually when the Deployment image changes.

## 9. Kubernetes Resources
### 9.1 Deployment
The Deployment declares two replicas and uses a RollingUpdate strategy. It defines resource requests and limits, and configures readiness and liveness HTTP probes.

### 9.2 Service
The Service selects Pods with the `app: devops-demo-web` label and forwards traffic to port 3000. The NodePort service type makes it accessible through Minikube's service helper.

### 9.3 Application
The Node.js app displays its version on the homepage and responds with `healthy` on `/health`.

## 10. Implementation Steps
1. Install Docker, Minikube, and kubectl.
2. Extract the project ZIP.
3. Start Minikube:
   ```bash
   minikube start
   ```
4. Configure the Docker environment to build images inside Minikube:
   ```bash
   eval $(minikube docker-env)
   ```
   On Windows PowerShell, use the Minikube Docker environment command appropriate to the shell, or build and load the image with `minikube image build`.
5. Build the first image:
   ```bash
   docker build -t devops-demo-web:v1 .
   ```
6. Apply the Kubernetes manifests:
   ```bash
   kubectl apply -f k8s/
   ```
7. Check the Pods, Deployment, and Service:
   ```bash
   kubectl get pods
   kubectl get deployments
   kubectl get services
   ```
8. Open the application:
   ```bash
   minikube service devops-demo-web
   ```

## 11. Rolling Update Demonstration
1. Change the app version to `v2` in the app configuration or Dockerfile.
2. Build a new image:
   ```bash
   docker build -t devops-demo-web:v2 .
   ```
3. Update the Deployment image:
   ```bash
   kubectl set image deployment/devops-demo-web web=devops-demo-web:v2
   ```
4. Watch the rollout:
   ```bash
   kubectl rollout status deployment/devops-demo-web
   ```
5. Inspect rollout history:
   ```bash
   kubectl rollout history deployment/devops-demo-web
   ```
6. Refresh the application and verify the displayed version.

## 12. Rollback
If the new release is not working as expected, run:
```bash
kubectl rollout undo deployment/devops-demo-web
kubectl rollout status deployment/devops-demo-web
```
Verify that the previous version is serving requests.

## 13. Expected Result
The cluster should run two application Pods managed by one Deployment. The Service should expose the app. The `/health` endpoint should return `healthy`, and the homepage should display the configured application version. A rolling update should replace the old Pods gradually, and rollback should restore the previous Deployment revision.

## 14. Testing and Verification
| Test case | Action | Expected result |
|---|---|---|
| Cluster status | Run `kubectl get nodes` | Minikube node is Ready |
| Pod availability | Run `kubectl get pods` | Two Pods become Running and Ready |
| Service | Run `kubectl get services` | Service is listed |
| Health check | Access `/health` | Returns `healthy` |
| Rolling update | Set image to v2 | Deployment completes successfully |
| Rollback | Run `kubectl rollout undo` | Previous revision is restored |

## 15. Advantages
- Demonstrates core Kubernetes objects.
- Shows how multiple replicas are managed.
- Introduces application health probes.
- Provides hands-on rolling update and rollback practice.
- Runs locally without requiring a managed Kubernetes service.

## 16. Limitations
- Uses a local Minikube cluster rather than production infrastructure.
- Does not include ingress, TLS, persistent volumes, or autoscaling.
- Image loading steps differ by operating system and Minikube driver.
- The example app is intentionally minimal.

## 17. Future Enhancements
- Add an Ingress controller and HTTPS.
- Configure Horizontal Pod Autoscaler.
- Add ConfigMaps and Secrets for configuration.
- Add resource monitoring with Prometheus and Grafana.
- Add CI/CD using GitHub Actions.
- Deploy to a managed Kubernetes service after learning cluster security and cost controls.

## 18. Conclusion
This project introduces container orchestration with Kubernetes. It demonstrates Deployments, Pods, Services, health probes, rolling updates, and rollbacks in a local environment, providing a foundation for further DevOps learning.

## 19. References
- Kubernetes documentation: https://kubernetes.io/docs/
- Minikube documentation: https://minikube.sigs.k8s.io/docs/
- Docker documentation: https://docs.docker.com/
- Node.js documentation: https://nodejs.org/docs/
