

---

# ☁️ MLflow on AWS – My Personal Setup Guide (with a little help from AI 🤖)

Hey there! 👋
This is a step-by-step guide I put together to help you set up your own **MLflow tracking server on AWS**. I built and tested this myself — with a little help from **ChatGPT** to organize the content and make it easier to follow.

If you're working with ML experiments and want to keep track of them on your own cloud setup, this guide is for you.

---

## 🔧 What You’ll Set Up

✅ A public **MLflow server** running on **Amazon EC2**
✅ An **S3 bucket** as the artifact storage backend
✅ A reusable setup for experiment tracking from anywhere

---

## 🛠️ Step-by-Step Guide

### 🪪 Step 1: Configure AWS

1. Log in to your [AWS Console](https://console.aws.amazon.com/).

2. Create an **IAM user** with `AdministratorAccess`.

3. On your local machine, run:

   ```bash
   aws configure
   ```

   Provide:

   * Your AWS Access Key ID
   * Secret Access Key
   * Default region (e.g., `us-east-1`)
   * Output format (optional)

4. Create a new **S3 bucket** to store MLflow artifacts
   (e.g., `mlflow-test-23`)

---

### 💻 Step 2: Launch EC2 and Prepare the Environment

1. Launch an **Ubuntu EC2 instance** (t2.micro is fine).

2. In the **Security Group**, allow:

   * **Port 5000** (for the MLflow UI)

3. Connect to the instance via SSH:

   ```bash
   ssh ubuntu@<your-ec2-public-dns>
   ```

4. Inside the EC2 instance, run:

   ```bash
   sudo apt update
   sudo apt install -y python3-pip virtualenv pipenv

   mkdir mlflow && cd mlflow

   pipenv install mlflow awscli boto3
   pipenv shell

   aws configure  # enter credentials again inside EC2
   ```

---

### 🚀 Step 3: Run the MLflow Server

Once everything is installed, launch your tracking server:

```bash
mlflow server \
  --host 0.0.0.0 \
  --port 5000 \
  --default-artifact-root s3://mlflow-test-23
```

Leave this running — it’s now serving your tracking UI!

---

### 🌍 Step 4: Access Your Server & Start Logging

1. In a browser, open:

   ```
   http://<your-ec2-public-dns>:5000
   ```

2. On your **local machine**, point your MLflow client to the server:

   ```bash
   export MLFLOW_TRACKING_URI=http://<your-ec2-public-dns>:5000
   ```

3. Run your MLflow code as usual — and your experiments will now be tracked remotely! 🧪

---

## 🧠 Extra Notes

* 🔒 **Security**: For real-world usage, consider securing your server with a reverse proxy (Nginx + HTTPS).
* 💸 **Cost Reminder**: Shut down or stop your EC2 when not using it.
* 📈 **Team Use**: Share the tracking URI so teammates can log runs too!

---

## 🙌 Why I Made This

I was exploring how to host my own MLflow server and decided to document the process clearly for future reference — and for anyone else who’s trying to do the same.

I used **perplexity** to help format and improve the guide. It made everything more readable and user-friendly. Highly recommend using tools like this when you want to turn raw notes into a clean walkthrough!

---

## 📚 Resources

* [MLflow Documentation](https://mlflow.org/docs/latest/index.html)
* [AWS EC2](https://aws.amazon.com/ec2/)
* [S3 Pricing](https://aws.amazon.com/s3/pricing/)
* [ChatGPT](https://chat.openai.com/)

---

If this guide helped you, feel free to ⭐️ the repo or open a PR to improve it! Happy tracking! 🎯

---
