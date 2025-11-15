# MedixInfo – Health Information Management System
**Domain:** medixinfo.org  
**Status:** Source code is stored in a **private repository**. This public documentation outlines the system design, cloud architecture, DevOps workflows, and engineering processes implemented by the Cloud/DevOps Engineer.

![AWS](https://img.shields.io/badge/AWS-Cloud%20Architecture-orange?logo=amazonaws)
![Docker](https://img.shields.io/badge/Docker-Containerized-blue?logo=docker)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Orchestrated-326ce5?logo=kubernetes)
![FastAPI](https://img.shields.io/badge/FastAPI-Backend-009688?logo=fastapi)
![NextJS](https://img.shields.io/badge/Next.js-Frontend-black?logo=next.js)
![CI/CD](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-blue?logo=githubactions)
![ECR](https://img.shields.io/badge/Amazon%20ECR-Registry-orange?logo=amazonaws)

---

## 🚀 Project Overview

**MedixInfo** is a cloud-native **Health Information Management System (HIMS)** that enables healthcare facilities to efficiently manage patient records, clinical workflows, authentication, and secure data exchange.

This README is intentionally public while the application source code remains in a private repository. The live system is reachable at: **https://medixinfo.org**

---

## 🖥️ Technology Stack

**Frontend**
- Next.js, React, TailwindCSS  
- Dockerized and orchestrated with Kubernetes  
- Horizontal Pod Autoscaling (HPA)  
- Container images hosted on Amazon ECR

**Backend**
- Python, FastAPI  
- Hosted on AWS EC2 instances within an Auto Scaling Group (ASG)  
- Application Load Balancer (ALB) for routing and TLS termination

---

## 🏗️ Architecture Diagram (SVG)
Below is the high-level architecture diagram (SVG). A downloadable copy is included next to this README.

<!-- Inline SVG starts -->
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<svg width="1000" height="520" xmlns="http://www.w3.org/2000/svg" version="1.1">
  <style>
    .title { font: bold 18px sans-serif; fill: #1f2937; }
    .label { font: 14px sans-serif; fill: #111827; }
    .box { fill: #ffffff; stroke: #334155; stroke-width:1.5; rx:8; }
    .cloud { fill: #eef2ff; stroke: #6366f1; stroke-width:1.5; rx:12; }
    .connector { stroke: #0f172a; stroke-width:1.2; marker-end: url(#arrow); }
  </style>

  <defs>
    <marker id="arrow" markerWidth="10" markerHeight="10" refX="8" refY="5" orient="auto">
      <path d="M0,0 L10,5 L0,10 z" fill="#0f172a" />
    </marker>
  </defs>

  <!-- Title -->
  <text x="20" y="30" class="title">MedixInfo – High-level Architecture</text>

  <!-- Users -->
  <rect x="30" y="60" width="160" height="40" class="cloud" />
  <text x="50" y="86" class="label">Users / Clients (Browser / Mobile)</text>

  <!-- Route53 / Domain -->
  <rect x="220" y="60" width="160" height="40" class="box" />
  <text x="250" y="86" class="label">Route 53<br/>medixinfo.org</text>

  <!-- ALB -->
  <rect x="420" y="60" width="160" height="40" class="box" />
  <text x="445" y="86" class="label">Application Load Balancer (ALB)</text>

  <!-- Kubernetes cluster -->
  <rect x="80" y="150" width="360" height="190" class="cloud" />
  <text x="90" y="175" class="label">Kubernetes Cluster (Frontend)</text>

  <!-- Frontend Pods box -->
  <rect x="110" y="200" width="140" height="110" class="box" />
  <text x="140" y="230" class="label">Next.js Frontend Pods</text>
  <text x="140" y="250" class="label">HPA (Horizontal Pod Autoscaler)</text>
  <text x="140" y="270" class="label">Images in Amazon ECR</text>

  <!-- ECR -->
  <rect x="500" y="220" width="160" height="60" class="box" />
  <text x="520" y="245" class="label">Amazon ECR</text>
  <text x="520" y="265" class="label">Docker Registry</text>

  <!-- EC2 ASG -->
  <rect x="500" y="340" width="160" height="80" class="box" />
  <text x="515" y="365" class="label">EC2 Auto Scaling Group</text>
  <text x="515" y="385" class="label">FastAPI Backend</text>

  <!-- Connectors -->
  <line x1="190" y1="80" x2="220" y2="80" class="connector" />
  <line x1="380" y1="80" x2="420" y2="80" class="connector" />
  <line x1="500" y1="100" x2="500" y2="220" class="connector" />
  <line x1="580" y1="140" x2="580" y2="220" class="connector" />
  <line x1="420" y1="100" x2="270" y2="150" class="connector" />
  <line x1="350" y1="260" x2="500" y2="260" class="connector" />
  <line x1="500" y1="300" x2="500" y2="340" class="connector" />

  <!-- Labels near connectors -->
  <text x="360" y="115" class="label">HTTPS / TLS</text>
  <text x="420" y="240" class="label">Ingress / Service</text>

  <!-- Footer note -->
  <text x="20" y="500" class="label">Live site: https://medixinfo.org</text>
</svg>

<!-- Inline SVG ends -->

---

## 🧰 My Responsibilities as Cloud/DevOps Engineer

- Designed and implemented the AWS architecture (Route 53, VPC, ALB, ASG).  
- Containerized the Next.js frontend and set up Kubernetes deployments with HPA.  
- Managed Docker images in Amazon ECR and automated image builds via CI.  
- Deployed FastAPI backend on EC2 instances with Auto Scaling and ALB integration.  
- Implemented monitoring (CloudWatch), health checks, and alerting.  
- Implemented secure HTTPS (SSL/TLS) and DNS configuration for **medixinfo.org**.  
- Ensured rolling updates and zero-downtime deployments.

---

## ⚙️ CI/CD (Example)

Example GitHub Actions workflow (frontend) — builds and pushes Docker image to ECR:

```yaml
name: Deploy Frontend to ECR

on:
  push:
    branches: [ "main" ]

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1

    - name: Login to Amazon ECR
      id: ecr
      uses: aws-actions/amazon-ecr-login@v1

    - name: Build Docker image
      run: |
        docker build -t medixinfo-frontend .

    - name: Tag image
      run: |
        docker tag medixinfo-frontend:latest ${{ steps.ecr.outputs.registry }}/medixinfo-frontend:latest

    - name: Push to ECR
      run: |
        docker push ${{ steps.ecr.outputs.registry }}/medixinfo-frontend:latest
```

---

## 📦 Deployment Summary

**Frontend**
1. Build Next.js app → Docker image → push to ECR  
2. Kubernetes Deployment pulls image → HPA scales pods as needed  
3. ALB or Ingress routes traffic to frontend

**Backend**
1. FastAPI service deployed on EC2 within an ASG  
2. ALB routes requests with health checks  
3. ASG scales EC2 instances based on metrics

---

## 🔐 Access & Contact

The repository is private. Access can be granted upon request.  
Live site: **https://medixinfo.org**

---

## 📄 License
MIT

