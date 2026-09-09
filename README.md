# Cloud-Native CI/CD Deployment Platform

A Flask-based REST API deployed automatically to an AWS EC2 instance using GitHub Actions, Docker, and Docker Hub.

The project demonstrates an end-to-end CI/CD pipeline that automatically tests the application, builds and versions a Docker image, pushes it to Docker Hub, deploys the image to EC2 over SSH, and verifies the application using a health check.

## Architecture

```text
Developer
    |
    | git push
    v
GitHub Repository
    |
    v
GitHub Actions
    |
    +----> Run Pytest
    |
    +----> Build Docker Image
    |
    +----> Tag Image with Git SHA
    |
    +----> Push Image to Docker Hub
    |
    v
Deploy Job
    |
    | SSH
    v
AWS EC2
    |
    +----> Pull Docker Image
    |
    +----> Remove Old Container
    |
    +----> Start New Container
    |
    +----> Health Check
    |
    v
Flask API
```

## Technologies Used

* Python
* Flask
* Pytest
* Docker
* Docker Hub
* GitHub Actions
* AWS EC2
* Linux
* Gunicorn
* Git & GitHub
* Bash

## Application

The project contains a simple Flask REST API with the following endpoints:

| Endpoint       | Description                 |
| -------------- | --------------------------- |
| `GET /`        | Returns API welcome message |
| `GET /health`  | Checks application health   |
| `GET /version` | Returns application version |

Example health response:

```json
{
  "status": "healthy"
}
```

## Docker

The application is containerized using Docker.

The Docker image:

* Uses Python 3.12
* Installs application dependencies
* Runs the Flask application using Gunicorn
* Exposes port `8000`

Example local execution:

```bash
docker build -t cloud-native-cicd:local .
```

```bash
docker run -d -p 8000:8000 --name cloud-native-api cloud-native-cicd:local
```

The API can then be accessed at:

```text
http://localhost:8000
```

## CI/CD Pipeline

The GitHub Actions workflow performs the following steps:

### 1. Checkout Code

GitHub Actions retrieves the latest source code.

### 2. Install Python Dependencies

The workflow installs the dependencies from `requirements.txt`.

### 3. Run Tests

Pytest automatically runs the application tests.

If the tests fail, the pipeline stops.

### 4. Build Docker Image

A Docker image is created using the current Git commit SHA.

```text
w616/cloud-native-cicd:<git-sha>
```

Using the Git SHA provides a unique version for every build.

### 5. Push Image to Docker Hub

The generated image is pushed to Docker Hub.

### 6. Deploy to AWS EC2

After the build job succeeds, the deployment job connects to the EC2 instance using SSH.

The server then:

1. Pulls the exact Docker image.
2. Removes the previous container.
3. Starts a new container using the new image.

### 7. Application Health Check

After deployment, the workflow waits briefly for the application to start and then checks:

```bash
curl http://localhost:8000/health
```

A successful response confirms that the application is running correctly.

## Deployment Dependency

The deployment job uses GitHub Actions `needs`:

```yaml
deploy-test:
  needs: test
```

This ensures that deployment does not start until testing, Docker image building, and Docker image pushing have successfully completed.

## Project Structure

```text
cloud-native-cicd/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── app/
│   ├── __init__.py
│   └── app.py
│
├── tests/
│   └── test_app.py
│
├── Dockerfile
├── pytest.ini
├── requirements.txt
├── README.md
└── .gitignore
```

## Running Locally

Clone the repository:

```bash
git clone <your-repository-url>
cd cloud-native-cicd
```

Create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run tests:

```bash
pytest
```

Run the application:

```bash
python app/app.py
```

The API will be available at:

```text
http://localhost:8000
```

## Deployment Requirements

To reproduce the deployment pipeline, you need:

* AWS EC2 instance
* Docker installed on EC2
* Docker Hub account and repository
* GitHub repository
* GitHub Actions enabled
* EC2 SSH access
* GitHub repository secrets for Docker Hub and EC2 deployment

## Key DevOps Concepts Demonstrated

* Continuous Integration
* Continuous Deployment
* Automated testing
* Docker containerization
* Container image versioning
* Docker registry
* GitHub Actions
* SSH-based deployment
* AWS EC2 deployment
* Linux server administration
* Application health verification
* CI/CD job dependencies

## Future Improvements

Potential improvements include:

* Docker image security scanning with Trivy
* Automated health-check retries
* Infrastructure provisioning with Terraform
* HTTPS using a reverse proxy and TLS
* AWS ECR integration
* Kubernetes deployment
* Monitoring with AWS CloudWatch
* Deployment rollback strategy

## Author

**Mohd Ahmad**

B.Tech Computer Science & Engineering

This project was built to develop practical Cloud and DevOps engineering skills through hands-on implementation of a complete CI/CD deployment workflow.
