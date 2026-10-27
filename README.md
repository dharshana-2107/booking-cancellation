## Build and Test

This project uses Maven to build and test the Campus Equipment Booking application.

### Prerequisites
- Java 17
- Maven
- Docker
- Minikube
- kubectl

### Run Tests

```bash
mvn clean test
```

### Build the Application

```bash
mvn clean package -DskipTests
```

The generated JAR file will be available in the `target/` directory.

## Docker Setup

### Build the Docker Image

Build the image from the project root:

```bash
docker build -t campus-equipment-booking:set-b .
```

### Run the Container

```bash
docker run -d \
  --name campus-booking-set-b \
  -p 8080:8080 \
  campus-equipment-booking:set-b
```

The application will be available at:

`http://localhost:8080`

### Check Application Health

Open the following URL:

`http://localhost:8080/actuator/health`

A healthy application returns a response similar to:

```json
{
  "status": "UP",
  "groups": ["liveness", "readiness"]
}
```

### Stop and Remove the Container

```bash
docker rm -f campus-booking-set-b
```

## Kubernetes Deployment

The project includes `deployment.yaml` and `service.yaml` for deploying the application to Kubernetes.

### 1. Start Minikube

```bash
minikube start
```

### 2. Load the Docker Image into Minikube

If the image was built using the host Docker daemon, load it into Minikube:

```bash
minikube image load campus-equipment-booking:set-b
```

### 3. Apply the Kubernetes Manifests

From the project root, run:

```bash
kubectl apply -f deployment.yaml -f service.yaml
```

### 4. Scale the Deployment

Run two application replicas:

```bash
kubectl scale deployment/campus-equipment-booking --replicas=2
```

### 5. Verify the Deployment

Check the deployment status:

```bash
kubectl rollout status deployment/campus-equipment-booking
```

Check the pods and service:

```bash
kubectl get deployments
kubectl get pods -o wide
kubectl get services
```

### 6. Access the Application

Retrieve the service URL:

```bash
minikube service campus-equipment-booking-service --url
```

Append `/actuator/health` to the returned URL to check the application's health.

For example, if Minikube returns `http://192.168.49.2:32076`, the health-check URL is:

`http://192.168.49.2:32076/actuator/health`

The IP address and NodePort may differ between environments.

## CI/CD Pipeline

The Jenkins pipeline automates the following stages:

1. Verify the source code and Git branch.
2. Run Maven tests.
3. Package the application into a JAR.
4. Build the Docker image.
5. Run the Docker container.
6. Check application health.
7. Load the Docker image into Minikube.
8. Deploy the application to Kubernetes.
9. Verify the deployment, pods, service, and application health.

The pipeline uses a two-replica Kubernetes deployment to improve availability.

## Assigned Change Request: Booking Cancellation

The assigned change request is **booking cancellation**. This change focuses on allowing users to cancel an existing equipment booking, subject to the application's implemented cancellation rules.

The feature is part of the `feature/booking-cancellation` branch.

## Troubleshooting

- **Maven tests fail:** Review the test output and resolve the reported errors.
- **Docker build fails:** Verify that Docker is running and that the required base image is available.
- **ImagePull errors in Kubernetes:** Confirm that the image is loaded into Minikube and that the deployment uses `imagePullPolicy: IfNotPresent`.
- **Application health check fails:** Check the container logs and Kubernetes pod status.

Useful commands:

```bash
docker logs campus-booking-set-b
kubectl describe deployment campus-equipment-booking
kubectl get pods -o wide
kubectl get endpoints campus-equipment-booking-service
```