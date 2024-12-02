# Deploying Allure Test Reports on Kubernetes with Docker Images

In today’s fast-paced DevOps environment, generating and visualizing test reports efficiently is critical for maintaining code quality. Allure, a popular test reporting framework, provides rich, interactive visualizations of test results. This article explains how to deploy Allure test reports using Docker images from fescobar’s Allure Docker Service and Kubernetes.

## Why Allure?
Allure provides beautiful, easy-to-navigate test reports that help teams visualize test results, track failures, and analyze performance trends. Integrating Allure into CI/CD pipelines, like GitHub Actions, ensures that testing is seamlessly automated and accessible.

## Step 1: Generating Allure Reports with GitHub Actions
First, ensure your CI/CD pipeline is set up to generate Allure-compatible test results. Allure works with various testing frameworks, such as TestNG, JUnit, Cucumber, and Pytest. For example projects, check out the Allure Docker Service Examples, including:

Java Examples: TestNG, JUnit, Cucumber
Node.js Examples: Cucumber, Mocha
Python Examples: Behave, Pytest
C# Examples: SpecFlow
Once your tests run in GitHub Actions, ensure they generate results in the allure-results folder. These results will later be processed by the Allure Docker Service.

## Step 2: Building and Pushing Docker Images in GitHub Actions
We’ll use two Docker images to generate and serve the reports:

Allure UI Image (frankescobar/allure-docker-service-ui): Provides the web interface for viewing reports.
Allure Backend Image (frankescobar/allure-docker-service): Processes the test results and generates reports.
In your GitHub Actions workflow:

Build these images using Dockerfile.
Push the images to Google Container Registry (GCR) or your preferred registry.
Example GitHub Actions workflow snippet for building and pushing:

```yaml

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v2

      - name: Set up Docker
        uses: docker/setup-buildx-action@v1

      - name: Build and push Allure UI Docker image
        run: |
          docker build -t gcr.io/${{ secrets.GCP_PROJECT_ID }}/allureui .
          docker push gcr.io/${{ secrets.GCP_PROJECT_ID }}/allureui

      - name: Build and push Allure Backend Docker image
        run: |
          docker build -t gcr.io/${{ secrets.GCP_PROJECT_ID }}/allure .
          docker push gcr.io/${{ secrets.GCP_PROJECT_ID }}/allure

```

## Step 3: Deploying Allure on Kubernetes
Deploy both the UI and backend containers in the same pod for ease of communication. Use a Kubernetes deployment YAML file (allure-report.yaml) for this purpose.

Kubernetes Deployment Configuration
In allure-report.yaml

Key Configurations
ALLURE_DOCKER_PUBLIC_API_URL: Ensure you set the environment variable to your domain URL (e.g., www.yourdomain.com).

kubectl Command: Apply the deployment with the following command:

kubectl apply -f k8s/allure-report.yaml -n ${namespace}

Routing Traffic
Ensure your domain or load balancer routes incoming traffic to the pod’s ports defined in the deployment file. This setup allows seamless access to the Allure report UI.
