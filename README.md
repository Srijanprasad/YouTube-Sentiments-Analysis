🎥 End-to-End YouTube Sentiment Analysis with CI/CD 🚀



Welcome to this complete ML project that collects YouTube comments, classifies sentiment using a trained model, and deploys the app on AWS using Docker & GitHub Actions!
This project integrates:

🧠 Machine Learning

📦 DVC for experiment tracking

☁️ AWS for deployment

🐳 Docker containers

🤖 GitHub Actions for CI/CD

🔑 YouTube Data API v3

🧪 Local Setup
1️⃣ Create and Activate Conda Environment
bash
Copy
Edit
conda create -n youtube python=3.11 -y
conda activate youtube
2️⃣ Install Requirements
bash
Copy
Edit
pip install -r requirements.txt
📂 DVC (Data Version Control)
We use DVC to manage datasets and model training pipelines.

bash
Copy
Edit
dvc init
dvc repro
dvc dag
🌐 FastAPI Inference API
Test your model with:

POST http://localhost:5000/predict

json
Copy
Edit
{
  "comments": [
    "This video is awesome! I loved it a lot.",
    "Very bad explanation. Poor video."
  ]
}
🔑 Get YouTube API Key (GCP)
Watch this tutorial

☁️ AWS CI/CD Deployment with GitHub Actions
Steps
Create IAM user with AmazonEC2FullAccess and AmazonEC2ContainerRegistryFullAccess

Create AWS ECR repo and save the URI

Launch Ubuntu EC2 instance and open necessary ports

Install Docker on EC2

Configure EC2 as GitHub self-hosted runner

Add GitHub secrets:

AWS_ACCESS_KEY_ID

AWS_SECRET_ACCESS_KEY

AWS_REGION

AWS_ECR_LOGIN_URI

ECR_REPOSITORY_NAME

🚀 GitHub Actions Workflow Example (.github/workflows/deploy.yml)
yaml
Copy
Edit
name: Deploy to AWS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: self-hosted

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ${{ secrets.AWS_REGION }}

      - name: Login to ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1

      - name: Build Docker image
        run: |
          docker build -t ${{ secrets.ECR_REPOSITORY_NAME }}:latest .

      - name: Tag Docker image
        run: |
          docker tag ${{ secrets.ECR_REPOSITORY_NAME }}:latest ${{ secrets.AWS_ECR_LOGIN_URI }}/${{ secrets.ECR_REPOSITORY_NAME }}:latest

      - name: Push Docker image to ECR
        run: |
          docker push ${{ secrets.AWS_ECR_LOGIN_URI }}/${{ secrets.ECR_REPOSITORY_NAME }}:latest

      - name: Deploy on EC2
        run: |
          ssh -o StrictHostKeyChecking=no ubuntu@your-ec2-ip << 'EOF'
          docker pull ${{ secrets.AWS_ECR_LOGIN_URI }}/${{ secrets.ECR_REPOSITORY_NAME }}:latest
          docker stop youtube || true
          docker rm youtube || true
          docker run -d --name youtube -p 5000:5000 ${{ secrets.AWS_ECR_LOGIN_URI }}/${{ secrets.ECR_REPOSITORY_NAME }}:latest
          EOF
🗑️ Recommended .gitignore
gitignore
Copy
Edit
# Python
__pycache__/
*.py[cod]
*.pyo
*.pyd
.env
venv/
env/
*.egg-info/
dist/

# Jupyter Notebook checkpoints
.ipynb_checkpoints/

# DVC
*.dvc
.dvc/cache/

# Docker
docker-compose.override.yml
.Dockerfile*

# AWS credentials (never commit!)
aws_credentials

# VSCode
.vscode/
🙌 Contribution & Support
Fork & star this repo if it helps you!

Report issues or suggest improvements via GitHub issues.



