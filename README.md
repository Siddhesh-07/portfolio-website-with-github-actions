# Portfolio Website — AWS S3 Static Hosting + GitHub Actions CI/CD

A personal portfolio website hosted on AWS S3 with automated deployments via GitHub Actions. Every push to `main` triggers a deployment to S3.

---

## 🏗️ Architecture

```
GitHub Repo
    │
    └── Push to main
            │
            ▼
    GitHub Actions (CI/CD)
            │
            └── aws s3 sync
                    │
                    ▼
            AWS S3 Bucket
        (Static Website Hosting)
            │
            ├── index.html  → Portfolio page
            └── error.html  → Custom error page
```

---

## 🚀 Features

- Static portfolio site hosted on AWS S3
- Automated CI/CD pipeline using GitHub Actions
- Custom error page for failed/missing routes
- Public access via S3 static website endpoint

---

## 🛠️ Setup Guide

### 1. Create S3 Bucket

- Go to AWS S3 → Create bucket
- Uncheck **Block all public access**
- Enable **Static website hosting** under Properties
  - Index document: `index.html`
  - Error document: `error.html`

### 2. Add Bucket Policy

Attach this policy under **Permissions → Bucket Policy**:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
    }
  ]
}
```

### 3. Configure GitHub Secrets

Add these secrets to your GitHub repo under **Settings → Secrets and variables → Actions**:

| Secret | Description |
|--------|-------------|
| `AWS_ACCESS_KEY_ID` | IAM user access key |
| `AWS_SECRET_ACCESS_KEY` | IAM user secret key |
| `S3_BUCKET_NAME` | Your bucket name |

### 4. GitHub Actions Workflow

The workflow file is at `.github/workflows/deploy.yml` and runs on every push to `main`.

---

## 📁 Project Structure

```
├── index.html              # Main portfolio page
├── error.html              # Custom 500/error page
├── .github/
│   └── workflows/
│       └── deploy.yml      # CI/CD pipeline
└── README.md
```

---

## ⚙️ GitHub Actions Workflow

`.github/workflows/deploy.yml`:

```yaml
name: Portfolio Deployment

on:
  push:
    branches:
    - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v1

    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1

    - name: Deploy static site to S3 bucket
      run: aws s3 sync . s3://webpage-with-git-actions --delete

```

> **Note:** The `--delete` flag removes files from S3 that are no longer in the repo. Make sure all files you want to keep (like `error.html`) are committed to the repo.

---

## 🌐 Live URL

Access the site via the S3 static website endpoint:

```
http://webpage-with-git-actions.s3-website-us-east-1.amazonaws.com
```

---

## 🔮 Possible Improvements

- [ ] Add **CloudFront** CDN for HTTPS and faster delivery
- [ ] Connect a custom domain via **Route 53**
- [ ] Add **cache invalidation** step in GitHub Actions after deploy
- [ ] Set up **S3 versioning** for rollback support

---

## 🧰 Tech Stack

| Tool | Purpose |
|------|---------|
| AWS S3 | Static file hosting |
| GitHub Actions | CI/CD pipeline |
| HTML/CSS/JS | Frontend |
