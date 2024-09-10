# Nginx Deployment with ConfigMap, Service, and Caching

## Overview

This setup deploys an Nginx server in a Kubernetes (K3s) environment. It uses a **ConfigMap** for custom configuration, **proxies** specific requests, and implements **caching** to improve performance.

## What it Does

- **Proxies requests**: Nginx forwards requests for a text file and an image from specific URLs.
- **Caching**: Stores upstream responses to speed up repeated requests and reduce server load.

## How it Works

1. **ConfigMap**: Holds Nginx proxy configuration, which is mounted in the pod as a volume.
2. **Deployment**: Runs the Nginx container using the provided configuration.
3. **Service**: Exposes Nginx externally using a `LoadBalancer` on port 80.
4. **Caching**: Nginx caches upstream responses to improve performance.

## How to Use It

1. **Apply Namespace**: 
   ```bash
   kubectl apply -f namespace.yaml
   ```

2. **Apply ConfigMap**: 
   ```bash
   kubectl apply -f configmap.yaml
   ```

3. **Deploy Nginx**: 
   ```bash
   kubectl apply -f deployment.yaml
   ```

4. **Expose Nginx via Service**: 
   ```bash
   kubectl apply -f service.yaml
   ```

5. **Verify the Setup**:
   - Check the running pods: `kubectl get pods -n nginx`
   - Get the external IP: `kubectl get svc -n nginx`

6. **Access the Application**:
   - For the text file: `http://<EXTERNAL_IP>/`
   - For the image: `http://<EXTERNAL_IP>/image`

## Conclusion

This Nginx deployment efficiently proxies and caches requests in Kubernetes using a custom configuration, making it accessible externally while optimizing performance with caching.
