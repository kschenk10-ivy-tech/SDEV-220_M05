# Module 5 Lab - Case Study: Containers and CI/CD



The purpose of this assignment is to learn about Docker and CI technologies

## Prerequisite:

Create a GitLab account at https://about.gitlab.com/

You will watch the following videos and perform the activities:

[Docker and PythonLinks to an external site.](https://www.youtube.com/watch?v=jtBVppyfDbE&ab_channel=TheDigitalLife)

## Instructions

### Complete the following steps:

-   Navigate to the following link to get familiar with Gitlab and deployment -  [CONTINUOUS INTEGRATION WITH GITLAB CI AND DOCKERLinks to an external site.](https://codebabel.com/ci-gitlabci-docker/)
    -   ADDITIONAL RESOURCES:
        -   -   -   https://docs.gitlab.com/ee/ci/docker/using_docker_images.html
                -   [https://youtu.be/qP8kir2GUgo?si=0IPwIooTf70z5U5VLinks to an external site.](https://youtu.be/qP8kir2GUgo?si=0IPwIooTf70z5U5V)[![](https://ivylearn.ivytech.edu/images/play_overlay.png)](https://youtu.be/qP8kir2GUgo?si=0IPwIooTf70z5U5V)
                -   [https://spacelift.io/blog/docker-ci-cdLinks to an external site.](https://spacelift.io/blog/docker-ci-cd)
                -   [https://www.techworld-with-nana.com/post/gitlab-ci-cd-for-beginnersLinks to an external site.](https://www.techworld-with-nana.com/post/gitlab-ci-cd-for-beginners)
                -   [https://dev.to/arbythecoder/how-to-set-up-a-cicd-pipeline-with-gitlab-a-beginners-guide-46b9Links to an external site.](https://dev.to/arbythecoder/how-to-set-up-a-cicd-pipeline-with-gitlab-a-beginners-guide-46b9)
        
-   When you get to editing the gitlab-ci.yml you may need to replace this line:
    -   - docker login -u gitlab-ci-token -p $CI_BUILD_TOKEN [registry.gitlab.com Links to an external site.](http://registry.gitlab.com/)
        
    -   With this line
    -   docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY

### What To Turn In

-   After completing/reading the above, you may turn in your assignments the following ways:  
    -   Take a screenshot of the build logs and paste them in a Word/Google Document.




---

# NOTES:

### **Docker** (Containerization)

Docker is a **containerization platform** that helps you package applications with all dependencies so they run consistently across different environments.

#### **Key Concepts**

-   **Containers**: Lightweight, standalone, and executable software packages that include everything needed to run an application (code, libraries, dependencies).
-   **Images**: Snapshots of a container that can be versioned and shared.
-   **Dockerfile**: A script defining how a Docker image is built.
-   **Docker Compose**: A tool for defining and running multi-container Docker applications.
-   **Volumes**: Persistent storage for data that needs to survive container restarts.

#### **Why Use Docker?**

-   **Portability**: Runs on any system with Docker installed.
-   **Consistency**: Eliminates "it works on my machine" problems.
-   **Scalability**: Easily deploy apps across different environments (local, cloud, production).
-   **Efficiency**: Uses fewer resources than virtual machines.

----------

### **Continuous Integration (CI)**

CI is a **development practice** where code changes are automatically built, tested, and merged multiple times a day.

#### **Key CI Technologies**

-   **CI/CD Pipelines**: Automate code integration (CI) and deployment (CD).
-   **Version Control Integration**: Works with Git, GitHub, GitLab, Bitbucket.
-   **Automated Testing**: Runs unit, integration, and functional tests.
-   **Build Automation**: Compiles code, checks dependencies, and creates artifacts.

#### **Popular CI/CD Tools**

-   **Jenkins** (Highly customizable, open-source)
-   **GitHub Actions** (Built into GitHub, good for open-source)
-   **GitLab CI/CD** (Integrated into GitLab repositories)
-   **CircleCI** (Cloud-based, fast, and developer-friendly)
-   **Travis CI** (Simple setup for open-source projects)
-   **Azure DevOps** (Enterprise-level CI/CD)

#### **Why Use CI?**

-   **Faster Development**: Developers integrate and test code frequently.
-   **Automated Testing**: Catches bugs early.
-   **Reliability**: Ensures stable builds before deployment.
-   **Better Collaboration**: Teams work on different features without breaking the main project.

----------

### **Docker + CI/CD Integration**

1.  **Containerized Builds**: Use Docker images to create consistent build environments.
2.  **CI Pipelines with Docker**: Automate builds and tests inside Docker containers.
3.  **Continuous Deployment (CD)**: Deploy updates automatically using Dockerized environments.

To set up a **Dockerized CI/CD pipeline**, let's break it down into key steps. Since you're using **Node.js with Express and MongoDB** for your student-teacher system and potentially other tech stacks for different projects, I'll keep it general but flexible for your needs.

----------

## **1️⃣ Setting Up Docker for Your App**

You'll need:

-   A **Dockerfile** to containerize your app.
-   A **docker-compose.yml** file (if using multiple services like MongoDB).

### **Example: Dockerfile (for a Node.js app)**

```dockerfile
# Use an official Node.js runtime as a parent image
FROM node:18-alpine

# Set the working directory in the container
WORKDIR /app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy the rest of the application
COPY . .

# Expose the port
EXPOSE 3000

# Command to start the application
CMD ["node", "server.js"]

```

----------

### **Example: docker-compose.yml (if using MongoDB)**

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - mongodb
  mongodb:
    image: mongo:6
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db

volumes:
  mongo-data:

```

-   Run `docker-compose up --build` to start both services.

----------

## **2️⃣ Setting Up a CI/CD Pipeline**

Your CI/CD tool (GitHub Actions, GitLab CI/CD, CircleCI, etc.) will:

1.  **Build** and test your app inside a Docker container.
2.  **Push** the Docker image to a container registry (Docker Hub, GitHub Container Registry, AWS ECR, etc.).
3.  **Deploy** to a server or cloud provider.

----------

### **Example: GitHub Actions CI/CD Pipeline**

Create `.github/workflows/docker-ci.yml` in your repo:

```yaml
name: Docker CI/CD Pipeline

on:
  push:
    branches:
      - main  # Run pipeline when pushing to main branch

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout Code
      uses: actions/checkout@v4

    - name: Log in to Docker Hub
      run: echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u "${{ secrets.DOCKER_USERNAME }}" --password-stdin

    - name: Build Docker Image
      run: docker build -t your-dockerhub-username/your-app-name .

    - name: Push to Docker Hub
      run: docker push your-dockerhub-username/your-app-name

  deploy:
    needs: build-and-push
    runs-on: ubuntu-latest

    steps:
    - name: SSH into Server and Deploy
      uses: appleboy/ssh-action@v0.1.10
      with:
        host: ${{ secrets.SERVER_IP }}
        username: ${{ secrets.SERVER_USER }}
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        script: |
          docker pull your-dockerhub-username/your-app-name
          docker stop my_app_container || true
          docker run -d --rm --name my_app_container -p 3000:3000 your-dockerhub-username/your-app-name

```

----------

## **3️⃣ Automating Tests in CI/CD**

Add automated tests before deployment:

### **Example: Run Jest Tests in GitHub Actions**

Modify `.github/workflows/docker-ci.yml`:

```yaml
    - name: Run Tests
      run: npm test

```

Ensure your `package.json` has:

```json
"scripts": {
  "test": "jest"
}

```

----------

## **4️⃣ Deployment Options**

Once your image is pushed, you can:

-   Deploy to **AWS EC2**, **DigitalOcean**, **Railway**, etc.
-   Use **Kubernetes** if scaling is needed.

----------

Great choice! **GitLab CI/CD** integrates directly with your GitLab repository, allowing you to automate builds, tests, and deployments using Docker. Below is a **step-by-step guide** to setting up a **GitLab CI/CD pipeline** with Docker.

----------

## **1️⃣ Enable GitLab CI/CD for Your Repository**

-   In your GitLab project, go to **Settings → CI/CD**.
-   Expand **Runners** and ensure GitLab has available runners.

----------

## **2️⃣ Create a `.gitlab-ci.yml` Pipeline**

This file defines how GitLab CI/CD will build, test, and deploy your app.

### **Example: `.gitlab-ci.yml`**

```yaml
image: docker:latest

services:
  - docker:dind  # Runs Docker in Docker

stages:
  - build
  - test
  - push
  - deploy

variables:
  DOCKER_IMAGE: "your-dockerhub-username/your-app-name"
  DOCKER_TAG: "latest"

before_script:
  - docker login -u "$DOCKER_USERNAME" -p "$DOCKER_PASSWORD"

build:
  stage: build
  script:
    - docker build -t $DOCKER_IMAGE:$DOCKER_TAG .
    - docker save -o app.tar $DOCKER_IMAGE:$DOCKER_TAG
  artifacts:
    paths:
      - app.tar

test:
  stage: test
  script:
    - echo "Running tests..."
    - docker load -i app.tar
    - docker run --rm $DOCKER_IMAGE:$DOCKER_TAG npm test

push:
  stage: push
  script:
    - docker push $DOCKER_IMAGE:$DOCKER_TAG

deploy:
  stage: deploy
  only:
    - main
  script:
    - apk add --no-cache openssh
    - ssh -o StrictHostKeyChecking=no $DEPLOY_USER@$DEPLOY_HOST "
        docker pull $DOCKER_IMAGE:$DOCKER_TAG &&
        docker stop app_container || true &&
        docker run -d --rm --name app_container -p 3000:3000 $DOCKER_IMAGE:$DOCKER_TAG
      "

```

----------

## **3️⃣ Configure GitLab CI/CD Variables**

Go to **Settings → CI/CD → Variables** and add:

Variable Name
Value
`DOCKER_USERNAME`
Your Docker Hub username
`DOCKER_PASSWORD`
Your Docker Hub password
`DEPLOY_USER`
Your server SSH user
`DEPLOY_HOST`
Your server IP or domain
`SSH_PRIVATE_KEY`
Your private SSH key for server access

----------

## **4️⃣ How It Works**

1.  **Build**: Creates a Docker image of your app.
2.  **Test**: Runs your app in a container and executes tests.
3.  **Push**: Pushes the Docker image to Docker Hub.
4.  **Deploy**: SSHs into your server, pulls the image, and runs it.

----------

## **5️⃣ Deploying to a GitLab Container Registry**

If you prefer **GitLab’s container registry** instead of Docker Hub, modify the pipeline:

### **Use GitLab Registry Instead of Docker Hub**

```yaml
variables:
  DOCKER_IMAGE: "$CI_REGISTRY_IMAGE"
  DOCKER_TAG: "$CI_COMMIT_REF_SLUG"

before_script:
  - docker login -u "$CI_REGISTRY_USER" -p "$CI_REGISTRY_PASSWORD" $CI_REGISTRY

```

GitLab will store the image at:

```
registry.gitlab.com/your-username/your-repo:latest

```

----------




### **Automatic Rollbacks for Failed Deployments + Kubernetes or Serverless Deployment Setup 🚀**

Since you want **automatic rollbacks** and **Kubernetes or serverless deployment**, let's go step by step.

----------

# **1️⃣ Automatic Rollbacks in GitLab CI/CD**

### **Why?**

-   If a deployment fails, your pipeline should **automatically revert** to the last working version.
-   This ensures **high availability** and minimizes downtime.

----------

## **🔹 Option 1: Rollback with Docker Containers**

If your **deployment fails**, the script will:

-   Pull the **previously working image** from Docker Hub/GitLab Registry.
-   Restart the container with the last stable version.

### **Modify `.gitlab-ci.yml` for Automatic Rollback**

```yaml
deploy:
  stage: deploy
  only:
    - main
  script:
    - apk add --no-cache openssh
    - ssh -o StrictHostKeyChecking=no $DEPLOY_USER@$DEPLOY_HOST "
        docker pull $DOCKER_IMAGE:$DOCKER_TAG || docker pull $DOCKER_IMAGE:previous &&
        docker stop app_container || true &&
        docker run -d --rm --name app_container -p 3000:3000 $DOCKER_IMAGE:$DOCKER_TAG || docker run -d --rm --name app_container -p 3000:3000 $DOCKER_IMAGE:previous
      "
  after_script:
    - docker tag $DOCKER_IMAGE:$DOCKER_TAG $DOCKER_IMAGE:previous
    - docker push $DOCKER_IMAGE:previous

```

### **How it Works**

1.  If the new deployment **fails**, GitLab will pull the last working `previous` tag and start it.
2.  If deployment **succeeds**, it tags the new version as `previous`.

----------

## **🔹 Option 2: Rollback with Kubernetes**

If you’re using **Kubernetes**, you can set up **automatic rollbacks using Helm**.

Modify the **deploy stage** in `.gitlab-ci.yml`:

```yaml
deploy:
  stage: deploy
  script:
    - kubectl apply -f k8s/deployment.yaml || kubectl rollout undo deployment my-app

```

-   If `kubectl apply` fails, Kubernetes will **automatically roll back** to the last successful deployment.

----------

## **2️⃣ Deploying with Kubernetes (K8s)**

Since you’re considering Kubernetes, you need:

-   A **Kubernetes cluster** (EKS on AWS, GKE on Google Cloud, or self-hosted).
-   A **Helm chart** (optional) for better deployment management.
-   A **k8s Deployment YAML file** to define your app.

----------

### **🔹 Example: Kubernetes Deployment (`k8s/deployment.yaml`)**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2  # Adjust replicas for scaling
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: your-dockerhub-username/your-app-name:latest
        ports:
        - containerPort: 3000
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
        envFrom:
        - configMapRef:
            name: app-config
---
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer

```

### **How It Works**

-   Deploys your app using **Docker image** from GitLab/Docker Hub.
-   Creates a **Kubernetes LoadBalancer** to expose the app.
-   Uses **auto-scaling** (`replicas: 2` for redundancy).

----------

## **🔹 Updating GitLab CI/CD for Kubernetes**

Modify `.gitlab-ci.yml`:

```yaml
deploy:
  stage: deploy
  script:
    - kubectl apply -f k8s/deployment.yaml
    - kubectl rollout status deployment my-app || kubectl rollout undo deployment my-app

```

### **What Happens?**

-   **`kubectl apply`** deploys the app.
-   **`kubectl rollout status`** checks if the deployment succeeded.
-   If it **fails**, **GitLab automatically rolls back** the last stable deployment.

----------

# **3️⃣ Serverless Deployment**

If you want **serverless deployment** (no managing servers!), use: ✅ **AWS Lambda**  
✅ **Google Cloud Run**  
✅ **Vercel** or **Netlify** (for frontend & serverless APIs)

----------

## **🔹 Deploying to AWS Lambda**

1.  **Package Your App for AWS Lambda**

-   Create a `handler.js` file:

```javascript
exports.handler = async (event) => {
  return {
    statusCode: 200,
    body: JSON.stringify({ message: "Hello from AWS Lambda!" }),
  };
};

```

2.  **Use GitLab CI/CD to Deploy to Lambda**

```yaml
deploy:
  stage: deploy
  script:
    - pip install awscli
    - aws lambda update-function-code --function-name myLambdaFunction --zip-file fileb://app.zip

```

----------

## **🔹 Deploying to Google Cloud Run**

1.  **Build a Docker Image**

```sh
gcloud builds submit --tag gcr.io/my-project/my-app

```

2.  **Deploy with GitLab CI/CD**

```yaml
deploy:
  stage: deploy
  script:
    - gcloud auth activate-service-account --key-file="$GOOGLE_APPLICATION_CREDENTIALS"
    - gcloud run deploy my-app --image=gcr.io/my-project/my-app --platform managed

```

----------
### **Kubernetes Deployment with GitLab CI/CD 🚀**

Since you’re going with **Kubernetes**, I’ll guide you through a **GitLab CI/CD pipeline** that deploys your app to a **Kubernetes cluster**, with automatic rollbacks on failure.

----------

# **1️⃣ Setting Up Your Kubernetes Cluster**

Before you can deploy, you need a **Kubernetes cluster**. You can use:  
✅ **Google Kubernetes Engine (GKE)**  
✅ **Amazon Elastic Kubernetes Service (EKS)**  
✅ **Azure Kubernetes Service (AKS)**  
✅ **Minikube** (for local testing)

**Install `kubectl` & `helm`** (if not already installed):

```sh
# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && sudo mv kubectl /usr/local/bin/

# Install Helm (optional, for better management)
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

```

----------

# **2️⃣ Creating Kubernetes Deployment Files**

You'll need **two YAML files**:

### **🔹 `k8s/deployment.yaml`**

Defines your app’s Kubernetes deployment.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2  # Adjust for scaling
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: registry.gitlab.com/your-username/your-repo:latest
        ports:
        - containerPort: 3000
        resources:
          limits:
            memory: "512Mi"
            cpu: "500m"
        envFrom:
        - configMapRef:
            name: app-config
---
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: LoadBalancer

```

### **🔹 `k8s/rollback.yaml`** _(For Rollbacks)_

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: rollback-job
spec:
  template:
    spec:
      containers:
      - name: rollback
        image: bitnami/kubectl
        command: ["kubectl", "rollout", "undo", "deployment", "my-app"]
      restartPolicy: Never

```

----------

# **3️⃣ GitLab CI/CD Pipeline (`.gitlab-ci.yml`)**

Modify your GitLab **CI/CD pipeline** to deploy using `kubectl`.

```yaml
image: google/cloud-sdk:latest

stages:
  - build
  - test
  - deploy
  - rollback

variables:
  K8S_NAMESPACE: "default"
  K8S_CLUSTER_NAME: "my-cluster"
  K8S_PROJECT: "my-gcp-project"
  K8S_REGION: "us-central1"

before_script:
  - echo "$KUBECONFIG_DATA" | base64 -d > kubeconfig.yaml
  - export KUBECONFIG=kubeconfig.yaml

build:
  stage: build
  script:
    - docker build -t registry.gitlab.com/your-username/your-repo:latest .
    - docker push registry.gitlab.com/your-username/your-repo:latest

test:
  stage: test
  script:
    - docker run --rm registry.gitlab.com/your-username/your-repo:latest npm test

deploy:
  stage: deploy
  script:
    - kubectl apply -f k8s/deployment.yaml
    - kubectl rollout status deployment my-app || exit 1

rollback:
  stage: rollback
  when: on_failure
  script:
    - kubectl apply -f k8s/rollback.yaml

```

----------

# **4️⃣ GitLab Secret Variables**

In **GitLab CI/CD → Settings → Variables**, add:

Variable Name

Description

`KUBECONFIG_DATA`

Base64-encoded Kubernetes config

`K8S_NAMESPACE`

Kubernetes namespace (e.g., `default`)

`K8S_CLUSTER_NAME`

Your Kubernetes cluster name

`K8S_PROJECT`

GCP/AWS project (if applicable)

`K8S_REGION`

Your cluster’s region

To encode your `kubeconfig` file:

```sh
cat ~/.kube/config | base64 -w 0

```

----------

# **5️⃣ How It Works**

✔ **Builds** a Docker image and pushes it to GitLab’s container registry.  
✔ **Tests** the application inside a container.  
✔ **Deploys** the new image to **Kubernetes**.  
✔ **Checks rollout status**. If deployment fails, it **automatically rolls back** to the last stable version.

----------


### Resources

**YouTube Video:**  
Lempa, C. (n.d.). _Docker VSCode Python tutorial // Run your app in a container_ [Video]. YouTube. Retrieved February 16, 2025, from [https://youtu.be/jtBVppyfDbE?si=HnRcwmuF_uvB7Otm](https://youtu.be/jtBVppyfDbE?si=HnRcwmuF_uvB7Otm)

**Web Article:**  
CodeBabel. (n.d.). _Continuous integration with GitLab CI and Docker._ Retrieved February 16, 2025, from [https://codebabel.com/ci-gitlabci-docker/](https://codebabel.com/ci-gitlabci-docker/)

**Documentation:**  
GitLab. (n.d.). _Run your CI/CD jobs in Docker containers._ Retrieved February 16, 2025, from [https://docs.gitlab.com/ee/ci/docker/using_docker_images.html](https://docs.gitlab.com/ee/ci/docker/using_docker_images.html)

**YouTube Video:**  
TechWorld with Nana. (n.d.). _GitLab CI/CD tutorial for beginners_ [Video]. YouTube. Retrieved February 16, 2025, from [https://www.youtube.com/watch?si=0IPwIooTf70z5U5V&v=qP8kir2GUgo&feature=youtu.be](https://www.youtube.com/watch?si=0IPwIooTf70z5U5V&v=qP8kir2GUgo&feature=youtu.be)

**Web Article:**  
Walker, J. (2024, April 17). _How to create a CI/CD pipeline with Docker._ Spacelift, Inc. Retrieved February 16, 2025, from [https://spacelift.io/blog/docker-ci-cd](https://spacelift.io/blog/docker-ci-cd)

**Blog Post:**  
TechWorld with Nana. (2023, April 6). _GitLab CI/CD for beginners in 1 hour._ nnSoftware GmbH. Retrieved February 16, 2025, from [https://www.techworld-with-nana.com/post/gitlab-ci-cd-for-beginners](https://www.techworld-with-nana.com/post/gitlab-ci-cd-for-beginners)

**Blog Post:**  
Arbythecoder. (2024, June 11). _How to set up a CI/CD pipeline with GitLab: A beginner's guide._ DEV Community. Retrieved February 16, 2025, from [https://dev.to/arbythecoder/how-to-set-up-a-cicd-pipeline-with-gitlab-a-beginners-guide-46b9](https://dev.to/arbythecoder/how-to-set-up-a-cicd-pipeline-with-gitlab-a-beginners-guide-46b9)
