# Minikube and Kubernetes Setup for CRS Project

This guide covers the setup and commands necessary for configuring Minikube, installing kubectl, and deploying the Course Recommendation System (CRS) components using Kubernetes.

## Prerequisites

- A Linux machine with `curl`, `chmod`, and `sudo` installed.
- Docker installed on your machine (for running Kubernetes nodes via Minikube).
- Basic understanding of Kubernetes and Docker concepts.

## 1. Install Minikube

Minikube allows you to run Kubernetes clusters locally.

### Download Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

- `curl`: A command-line tool to transfer data from or to a server, in this case, downloading Minikube.
- `-L`: Follow redirects.
- `-O`: Save the file with the same name as it has on the server.
- The URL points to the latest stable release of Minikube for Linux (AMD64 architecture).

### Install Minikube

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

- `sudo`: Run the command as a superuser (admin).
- `install`: Copies the downloaded binary to the `/usr/local/bin/` directory for global usage.
- `minikube-linux-amd64`: The downloaded Minikube binary.
- `/usr/local/bin/minikube`: Directory where the Minikube executable is installed, making it available globally.

### Start Minikube

```bash
minikube start
```

Initializes a local Kubernetes cluster using Minikube. This will either use a virtual machine or container to run Kubernetes.

## 2. Install kubectl

`kubectl` is the command-line tool used to interact with Kubernetes clusters.

### Download kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

- `curl`: Used to download the kubectl binary from the Kubernetes release repository.
- `-L`: Follow redirects.
- `-s`: Silent mode, suppressing progress output.
- `$(...)`: Command substitution to fetch the latest stable version of kubectl dynamically from the Kubernetes release endpoint.

### Make kubectl Executable

```bash
chmod +x kubectl
```

- `chmod +x`: Adds execute permissions to the kubectl binary, making it executable.

### Move kubectl to a Directory in the System PATH

```bash
sudo mv kubectl /usr/local/bin/
```

- `mv`: Moves the kubectl binary to `/usr/local/bin/`, which is in the system's PATH. This allows you to run kubectl globally.

### Verify the kubectl Version

```bash
kubectl version --client
```

Displays the version of the kubectl client. The `--client` flag ensures that only the client version is displayed, not the server version (since we’re not yet connected to a Kubernetes cluster).

## 3. Set the Kubernetes Context to Minikube

To interact with the Minikube cluster, you need to set the context.

```bash
kubectl config use-context minikube
```

- `kubectl config use-context`: Switches the current Kubernetes context to Minikube, which ensures kubectl commands are sent to the correct cluster.

### Get Nodes in the Cluster

```bash
kubectl get nodes
```

- `kubectl get nodes`: Lists all the nodes (virtual machines or containers) in your Kubernetes cluster.

### Get Services in the Cluster

```bash
kubectl get svc
```

- `kubectl get svc`: Lists all services in your Kubernetes cluster. Services provide stable access points to your application Pods.

## 4. Deploy Resources for CRS

### Apply Namespace

```bash
kubectl apply -f k8s/namespace.yaml
```

- `kubectl apply -f`: Creates or updates resources based on the YAML file.
- `k8s/namespace.yaml`: Defines a namespace for the CRS resources, helping organize resources within the cluster.

### Apply PostgreSQL Persistence Configuration

```bash
kubectl apply -f k8s/postgres/persistence/
```

Applies the persistence configuration for PostgreSQL, ensuring data is stored permanently even if Pods are recreated.

### Apply PostgreSQL Secrets

```bash
kubectl apply -f k8s/postgres/secrets/
```

- `secrets/`: Contains sensitive information like database credentials. Kubernetes stores these securely.

### Apply PostgreSQL Deployment

```bash
kubectl apply -f k8s/postgres/
```

- `k8s/postgres/`: This directory contains the configuration for deploying PostgreSQL, including Services, Deployments, and other resources.

### Apply Django Configuration

```bash
kubectl apply -f k8s/django/config/
```

Applies configuration settings for the Django application, such as environment variables or configuration files.

### Apply Django Deployment

```bash
kubectl apply -f k8s/django/
```

- `k8s/django/`: Contains the YAML files to deploy the Django application, including deployment, service, and other necessary resources.

### Apply React Application Deployment

```bash
kubectl apply -f k8s/react/
```

- `k8s/react/`: This directory contains the deployment configuration for the React frontend application.

### Apply Ingress Configuration

```bash
kubectl apply -f k8s/ingress/
```

- `k8s/ingress/`: Contains configurations for exposing services to external traffic using Kubernetes Ingress, which handles routing.

## 5. Access Minikube Dashboard

To view the Kubernetes dashboard, use the following command:

```bash
minikube dashboard
```

This command opens the Minikube dashboard in your web browser, providing a UI to monitor and manage your Kubernetes cluster.

## 6. Port Forwarding for React Service

To access the React application running in Kubernetes locally, use port forwarding:

```bash
kubectl port-forward service/react-service 8080:3000 -n crs-namespace
```

- `kubectl port-forward`: Forwards a local port (8080) to the React service running in the Kubernetes cluster on port 3000.
- `-n crs-namespace`: Specifies the namespace where the React service is deployed (use the correct namespace for your setup).

Now you can access the React application at [http://localhost:8080](http://localhost:8080).