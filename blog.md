*Overengineering a simple game to practice real-world DevOps skills*

---

## TL;DR

Yes, I know — nobody needs Kubernetes, Terraform, and ArgoCD to run 2048. It's overkill. But that's exactly why I did it. By deploying a simple static game with enterprise-grade tools, I could focus purely on **DevOps patterns** without being distracted by app complexity.

![2048 game in the cloud](https://raw.githubusercontent.com/HasanAshab/2048-game-devops/master/img/flow.png)

This project turned the classic 2048 game into a cloud-native demo running on AWS with:

* Docker + ECR for containerization
* Kubernetes on EKS for orchestration
* GitHub Actions for CI/CD
* ArgoCD for GitOps deployments
* Terraform for infrastructure as code

**🔗 [Source code on GitHub](#)**


## Why 2048?

The game itself is trivial — just static HTML/JS. But that's the point. By stripping away complicated backend logic, I was free to practice:

* Building a full pipeline from scratch
* Managing cloud infra with Terraform
* Deploying to Kubernetes
* Setting up GitOps with ArgoCD

It's not about the game. It's about showing that if you can deploy 2048 this way, you can deploy *any app* this way.

---

## What I Built

### 1. Containerized the Game

I packaged the 2048 codebase into a Docker image using `nginx:alpine` as a lightweight web server:

```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

This was pushed to **Amazon ECR**, ready for Kubernetes.

### 2. Kubernetes Deployment on EKS

I wrote manifests for:

* **Deployment**: 3 replicas for availability, with resource limits and probes.
* **Service**: Type `LoadBalancer` to expose via AWS Load Balancer.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: game-2048
spec:
  replicas: 3
  selector:
    matchLabels:
      app: game-2048
  template:
    metadata:
      labels:
        app: game-2048
    spec:
      containers:
      - name: game-2048
        image: <ECR_REPO>/game-2048:latest
        ports:
        - containerPort: 80
```

### 3. CI/CD with GitHub Actions

A workflow handles:

* Linting and basic tests
* Building + pushing Docker image to ECR
* Updating manifests

![GitHub Actions Workflow](https://raw.githubusercontent.com/HasanAshab/2048-game-devops/master/img/cicd.png)

This keeps the pipeline automated from commit → container → deployment.


### 4. GitOps with ArgoCD

Instead of manually applying manifests, I set up **ArgoCD** on EKS. It watches the Git repo and automatically syncs changes, giving me:

* Automated deployments
* Rollback capabilities
* A nice UI to visualize app state


### 5. Infrastructure with Terraform

Terraform provisions:

* VPC + subnets
* EKS cluster + node groups
* ECR repository

This makes the whole setup reproducible in any AWS account.

---

## Lessons Learned

* **GitOps feels powerful**: Letting ArgoCD drive deployments keeps clusters in sync with Git.
* **IaC saves time**: Rebuilding infra with Terraform is way easier than clicking through AWS console.
* **Overkill is okay for learning**: Practicing with simple apps helps when stakes are low.
* **Docker + EKS + ArgoCD** is a solid baseline stack for real-world apps.

## What's Next

If I revisit this project, I'd like to:

* Add monitoring (CloudWatch or Prometheus)
* Use Fargate for serverless pods
* Try spot instances for cost savings
* Add Route53 + SSL for a proper domain


## Final Thoughts

Running 2048 on AWS with Terraform, Kubernetes, and ArgoCD is complete overkill — and that's exactly the point. This project let me practice the same workflows companies use in production, but in a low-stakes, fun environment.

If you can take a tiny game and ship it with a full DevOps stack, you're already building the habits needed for larger, real-world systems.

---
## 📬 Contact
If you have any doubts, feel free to reach out and ask:

* **Website**: [hasan-ashab](https://hasan-ashab.vercel.app/)
* **LinkedIn**: [linkedin.com/in/hasan-ashab](https://linkedin.com/in/hasan-ashab/)