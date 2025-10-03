CI/CD Pipeline – GitHub Actions

This repository is configured with a GitHub Actions workflow that automates building, testing, scanning, and publishing our application to AWS Elastic Container Registry (ECR).

📌 Workflow Overview

The workflow file is located at:

.github/workflows/main.yml


It runs on the following triggers:

Push to the main branch

Pull Requests targeting the main branch

Manual trigger via workflow_dispatch

Scheduled runs at 14:10 UTC, Monday–Friday

🔄 Pipeline Stages
1. Build

Checks out the repository.

Runs Maven build: mvn install.

Uploads the generated .war file from the target/ directory as an artifact.

Sends a notification if the build fails.

2. Testing

Runs after the build stage.

Executes unit tests (mvn test) on the main branch.

Skips tests for non-main branches (to save CI time).

3. Security Scan

Runs after the build stage.

Uses Trivy to scan dependencies and OS libraries for vulnerabilities.

Exports results as trivy-results.json and uploads as an artifact.

4. Build & Publish to AWS ECR

Runs only on the main branch after Build, Testing, and Security Scan succeed.

Configures AWS credentials securely via GitHub Secrets.

Logs in to Amazon Elastic Container Registry (ECR).

Builds the Docker image using the multistage Dockerfile (Docker-files/app/multistage/Dockerfile).

Tags the image with the current commit SHA.

Pushes the image to the configured ECR Repository.

🔐 Secrets and Variables

The workflow uses the following GitHub Secrets/Variables:

AWS_ACCESS_KEY_ID – AWS access key

AWS_SECRET_ACCESS_KEY – AWS secret key

AWS_REGION – AWS region where ECR is hosted

ECR_REPOSITORY – Name of the ECR repository (vprofile-appimage)

⚙️ Docker Image

The Docker image is built from the multistage Dockerfile inside:

Docker-files/app/multistage/Dockerfile


The image is then tagged and pushed to ECR:

<aws_account_id>.dkr.ecr.<region>.amazonaws.com/vprofile-appimage:<commit-sha>

🛠️ Summary

This pipeline ensures:

✅ Code is built and tested automatically

✅ Security vulnerabilities are scanned and reported

✅ Docker images are built and pushed to AWS ECR

✅ Only production-ready builds from main are deployed

