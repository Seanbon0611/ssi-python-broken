# Single Step APM Instrumentation Exercise(Kubernetes)

This tutorial demonstrates how to instrument a Python app for APM (Application Performance Monitoring) in a Kubernetes environment using Single Step APM Instrumentation.

**Note: At the time of writing, Single Step APM Instrumentation for Kubernetes is in Preview status. Configurations may change once it reaches General Availability (GA).**

## Prerequisites
1. ### Minikube: If you don't have Minikube installed, follow the installation instructions in our knowledge base: [How to test Kubernetes yourself](https://datadoghq.atlassian.net/wiki/spaces/TS/pages/1248530082/How+to+test+Kubernetes+yourself).

2. ### Datadog API Key: Create a secret for your Datadog Agent:
   ```bash
   kubectl create secret generic datadog-agent --from-literal api-key="<your API key>"
    ```

## Getting Started:
1. ### Deploy your Agent & Cluster Agent:
    ```bash
    helm install datadog -f values.yaml datadog/datadog
    ```
2. ### Create a Namespace for Your Application:
    For Single Step APM Instrumentation to work correctly, your application pod must run in a namespace separate from the Datadog Agent pods. Create a new namespace:
    ```bash 
    kubectl create namespace ssi
    ```
3. ### Build the Docker Image:
    Before building the Docker image in Minikube, configure your shell to interact with the Docker daemon inside the Minikube cluster:
    ```bash
    eval $(minikube -p minikube docker-env)
    ```
    Navigate to the root directory and build the Docker image:
    ```bash
    docker build -t ssi-py-sandbox:latest ./app/
    ```

4. ### Deploy your application:
    Deploy the application using the Kubernetes manifest file:
    ```bash
    kubectl -n ssi apply -f app_deployment.yaml
    ```

5. ### Retrieve the Application Pod Name:
    Find the name of your application pod. It should follow the naming convention ssi-py-sandbox-xxxxxxxx-xxxx:
    ```bash
    kubectl -n ssi get pods
    ```

5. ### Generate Traffic
    Access the application pod and make a request to the root endpoint:
    ```bash
    kubectl exec -it <your application pod> -- bash
    curl localhost:5000
    ```

5. ### View Traces in Datadog:
    In the Datadog Web UI, you should see traces for the service under the configured service name.