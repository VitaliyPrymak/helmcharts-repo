
# Kubernetes Cluster Setup with Argo CD

This project involves setting up a local Kubernetes cluster using KinD (Kubernetes in Docker) and deploying various services using GitOps and Argo CD. Below is a description of the steps I followed to successfully deploy and manage the infrastructure.

## Project Overview

In this project, I created a Kubernetes cluster with KinD, installed Argo CD, and deployed various applications using Helm charts. Argo CD was used for managing the cluster in a GitOps fashion. Additionally, the project included setting up AWS services for secrets management and container image storage.

## Steps Taken

### 1. **KinD Cluster Setup**
   - Created a local Kubernetes cluster using KinD with 1 control-plane node and 2 worker nodes.
   - Used a pre-configured `kind-config.yaml` file to set up the cluster.

   Command to create the cluster:
   ```bash
   kind create cluster --config kind-config.yaml
   ```

### 2. **Argo CD Installation**
   - Installed Argo CD using the official Helm chart.
   - Deployed Argo CD into the `argocd` namespace and disabled certain features like Dex and Notifications.
   
   Command to install Argo CD:
   ```bash
   helm install argocd argo/argo-cd --version 7.8.2 -n argocd --create-namespace --set dex.enabled=false --set notifications.enabled=false
   ```

   - Retrieved the initial admin password and accessed the Argo CD UI via port forwarding:
   ```bash
   kubectl port-forward service/argocd-server -n argocd 8080:443
   ```

### 3. **GitHub Repository Setup**
   - Created a private GitHub repository to store Helm charts.
   - Set up a GitHub App to grant Argo CD access to the repository.
   - Configured the app with read-only access to contents and metadata.

### 4. **AWS Configuration**
   - Created AWS ECR public repositories for storing container images.
   - Set up AWS Secrets Manager to store sensitive information for development and production environments.
   - Configured AWS IAM roles and permissions for GitHub Actions to securely access AWS resources.

### 5. **Helm Charts Deployment via Argo CD**
   - Created Helm charts for backend and frontend services, as well as for PostgreSQL, Ingress NGINX, and External Secrets.
   - Deployed these applications using Argo CD, with Argo managing all the configurations in a GitOps fashion.
   - Set up the "App of Apps" pattern in Argo CD for managing multiple applications.

### 6. **PostgreSQL Setup with Argo CD ApplicationSet**
   - Created an Argo CD ApplicationSet for PostgreSQL, dynamically generating separate applications for the `dev` and `prod` environments.
   - Used Helm charts to deploy PostgreSQL with the necessary configurations for both environments.

### 7. **Creating Custom Helm Charts**
   - Developed custom Helm charts for both the backend and frontend applications.
   - Included Kubernetes manifests (deployment, service, ingress) in the Helm templates, ensuring reusability across environments.

### 8. **GitHub Actions for CI/CD**
   - Configured GitHub Actions to build Docker images, push them to AWS ECR, and update the Helm chart values with the new image tags.
   - Set up workflows for both backend and frontend applications, with environment-specific deployments.

### 9. **Argo CD Integration with External Secrets**
   - Configured Argo CD to deploy and manage the External Secrets controller, enabling Kubernetes to retrieve sensitive information from AWS Secrets Manager.

## Summary of Tools and Technologies Used
- **KinD**: For creating a local Kubernetes cluster.
- **Argo CD**: For GitOps-based management of Kubernetes resources.
- **Helm**: For deploying and managing applications.
- **AWS ECR**: For storing Docker images.
- **AWS Secrets Manager**: For securely managing secrets.
- **GitHub Actions**: For continuous integration and deployment (CI/CD).
- **External Secrets**: For securely accessing secrets from AWS Secrets Manager.

## Final Notes
The project is fully automated with Argo CD managing the deployment process. Each service is deployed based on GitOps principles, ensuring that the state of the Kubernetes cluster is always synchronized with the configurations stored in the GitHub repository.
