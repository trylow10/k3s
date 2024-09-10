# GitSync CI/CD Pipeline with Nginx Deployment

## Overview
This Kubernetes deployment demonstrates the setup of a **CI/CD pipeline** using **GitSync** and **Nginx**. The pipeline ensures that website content from a **self-hosted Git repository** is automatically synced and served by Nginx. The system runs in a dedicated namespace (`nginx`), exposing the service with an ingress resource for external access. The Nginx server reloads automatically to reflect changes in the repository without manual intervention.

---

## Kubernetes Resources

### 1. **Namespace**
A namespace isolates resources to prevent conflicts in the Kubernetes cluster. The `nginx` namespace is created to manage Nginx-related resources.

### 2. **Deployment**
The deployment consists of two containers:
- **git-sync**: Syncs files from a **self-hosted Git repository**.
- **nginx-resturan**: Serves the synced content using the Nginx web server.

#### Deployment Details:
- **GitSync container (`git-sync`)**:
  - Clones the repository from the self-hosted Git server (`http://gittry.com:3000/gogs/resturan.git`).
  - Syncs content into the `/data/html` folder.
  - Reloads Nginx automatically using the `nginx -s reload` command when changes are detected.

- **Nginx container (`nginx-resturan`)**:
  - Serves content from the `/usr/share/nginx/html` directory.
  - Uses a shared volume (`www-data`) to access the synced files.

### 3. **Service**
The **Service** exposes the Nginx application within the Kubernetes cluster and externally through NodePort, accessible via port `30002`.

### 4. **Ingress**
An **Ingress** resource exposes the Nginx service externally, routing traffic based on the `resturan.com` domain and providing SSL termination using a Kubernetes-managed TLS secret.

---

## GitSync CI/CD Workflow

### GitSync Container Workflow:
1. **Repository Sync**: The GitSync container continuously monitors the **self-hosted Git repository** (`http://gittry.com:3000/gogs/resturan.git`) for changes in the `master` branch.
2. **File Sync**: When changes are detected, the GitSync container pulls the latest content from the repository and stores it in the `/data` directory.
3. **Volume Sharing**: The synced content is placed in a shared volume (`/data`) and mounted into the Nginx container at `/usr/share/nginx/html`.
4. **Nginx Reload**: Once the content is synced, GitSync triggers an Nginx reload by executing `nginx -s reload`, ensuring that Nginx serves the updated content.

### Nginx Container Workflow:
1. **Serving Content**: The Nginx container serves the website from the `/usr/share/nginx/html` directory, which is automatically updated by the GitSync container.
2. **Automatic Reload**: When GitSync pulls new updates, Nginx is reloaded automatically to ensure minimal downtime and seamless updates to the web content.

---

## Summary of Key Features

- **Automatic Content Sync**: GitSync keeps the Nginx server up-to-date with the latest content from the **self-hosted Git repository**.
- **Minimal Downtime**: Nginx automatically reloads to serve new content when changes are detected.
- **Namespace Isolation**: Resources are isolated in the `nginx` namespace, preventing conflicts with other applications in the Kubernetes cluster.
- **External Access**: The Nginx service is accessible externally via NodePort and Ingress with secure TLS connections.

---

## How to Use

### 1. Deploy Resources:
Apply the provided YAML files to your Kubernetes cluster to deploy the application.

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
kubectl apply -f namespace.yaml
```

### 2. Access the Application:
Once deployed, the application will be accessible via the `resturan.com` domain (ensure proper DNS and TLS configuration for your environment).

### 3. Update Git Repository:
To update the website, commit and push changes to the **self-hosted Git repository** (`http://gittry.com:3000/gogs/resturan.git`). GitSync will automatically pull the updates, and Nginx will reload to reflect the new content.

---

## Conclusion
This deployment implements a simple CI/CD pipeline using GitSync and Nginx to automatically sync and serve content from a **self-hosted Git repository**. It ensures that your web server is always up-to-date with the latest changes and provides secure external access through Ingress with TLS termination.
