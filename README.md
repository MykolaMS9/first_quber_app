# Task: Create Kubernetes Configuration for a 3-Tier Application

**Objective:**

Develop a comprehensive set of Kubernetes manifests (`.yaml` files) to deploy, manage, and network a complete web application stack. The stack includes a persistent database, a backend API, and an Nginx server acting as a reverse proxy and ingress point.

---

### 1. Database Setup

The goal is to deploy a stateful database (e.g., PostgreSQL, MySQL, or MongoDB) with data persistence.

*   **`Secret`:** Create a Kubernetes Secret to securely store sensitive database credentials, such as the username, password, and database name. This avoids hardcoding sensitive information in configuration files.
*   **`PersistentVolumeClaim` (PVC):** Define a PVC to request persistent storage from the cluster. This ensures that database data survives pod restarts and failures.
*   **`StatefulSet`:** Use a `StatefulSet` to manage the database pod. `StatefulSet` is ideal for stateful applications as it provides stable network identifiers and ordered, graceful deployment and scaling.
*   **`Service`:** Create a `ClusterIP` Service to provide a stable internal endpoint for the database. This allows the backend API to connect to the database using a consistent DNS name within the cluster.

### 2. Backend API Setup

The goal is to deploy the stateless backend API and connect it to the database.

*   **`Dockerfile`:** Ensure the API has a `Dockerfile` to be built into a container image.
*   **`Deployment`:** Use a `Deployment` to manage the API pods. This allows for easy scaling, rolling updates, and self-healing (restarting failed pods).
*   **`Service`:** Create a `ClusterIP` Service to expose the API internally. This service will be the target for Nginx to route traffic to.
*   **Environment Variables:** Inject the database connection details (from the `Secret`) and the database service DNS name into the API pods as environment variables.
*   **(Optional) `HorizontalPodAutoscaler` (HPA):** Implement an HPA to automatically scale the number of API pods based on CPU or memory utilization to handle varying loads.

### 3. Nginx Setup (as Reverse Proxy/Ingress)

The goal is to configure Nginx to manage external access, routing incoming traffic to the backend API.

*   **`Deployment`:** Create a `Deployment` for the Nginx pods.
*   **`ConfigMap`:** Use a `ConfigMap` to store the `nginx.conf` file. This configuration will define the reverse proxy rules (e.g., `proxy_pass` to the API's internal service).
*   **`Service`:** Expose the Nginx deployment to the internet using a `LoadBalancer` or `NodePort` Service. A `LoadBalancer` is preferred for cloud environments as it automatically provisions an external load balancer.
*   **(Alternative/Recommended) `Ingress`:** For a more robust and standard setup, deploy an Ingress Controller (like Nginx Ingress Controller) and create an `Ingress` resource. The `Ingress` resource defines HTTP/S routing rules to direct traffic from specific hostnames or paths to the API's service, handling SSL/TLS termination and path-based routing cleanly.

### Summary of Kubernetes Objects to Create:

*   **Database:**
    *   `Secret`
    *   `PersistentVolumeClaim`
    *   `StatefulSet`
    *   `Service` (ClusterIP)
*   **API:**
    *   `Deployment`
    *   `Service` (ClusterIP)
    *   `HorizontalPodAutoscaler` (Optional)
*   **Nginx:**
    *   `Deployment`
    *   `ConfigMap`
    *   `Service` (LoadBalancer/NodePort)
    *   OR `Ingress` resource (with an Ingress Controller)
