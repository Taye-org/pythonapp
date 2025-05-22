 ## Jenkins CI/CD Pipeline with Docker, Snyk & GitHub Integration
This project demonstrates how to build a secure CI/CD pipeline using Jenkins, Docker, Snyk, and GitHub. The pipeline automates building, scanning, pushing, and deploying a Docker image containing a simple python flask app.

# Project Objectives
✅ Set up Jenkins and connect it to a GitHub repository

✅ Create a Jenkinsfile to define pipeline stages

✅ Build a Docker image from HTML app

✅ Scan the Docker image with Snyk for vulnerabilities

✅ Push the image to a container registry

✅ Deploy the Docker image to a remote VM (or Kubernetes cluster)

✅ Securely store and use credentials and avoid exposing secrets

# Project Structure

- ├── Dockerfile
- ├── Jenkinsfile
- ├── index.html  
- └── README.md
# Jenkinsfile Pipeline Stages
Build: Create a Docker image from the Dockerfile

Scan: Use Snyk to identify vulnerabilities in the image

Push: Upload the scanned (clean) image to Docker Hub

Deploy: SSH into a VM and deploy the container there

# Snyk Security Integration
1. Create a Snyk Account
Sign in and get your SNYK_TOKEN under Account Settings → API Token.

2. Install Snyk in Jenkins Host
sudo apt update
sudo apt install nodejs npm -y
npm install -g snyk
Authenticate locally (only once):
snyk auth
3. Add Snyk Token to Jenkins Credentials
Go to: Manage Jenkins > Credentials > Global

Add a new Secret Text

ID: SNYK_TOKEN

Secret: (Paste your token)

4. Use Snyk in Jenkinsfile
withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
    sh 'snyk auth $SNYK_TOKEN'
    sh 'snyk container test your-image-name'
}
🐳 Dockerfile (Sample)
Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
🚀 Deployment Notes
Used docker-compose for local Jenkins setup

Ngrok was used in earlier setups to expose Jenkins publicly

SSH keys were securely managed (not hardcoded in Jenkins)

VM deployment done via remote SSH and docker run

🔗 Related Tools
Jenkins

Docker

GitHub

Snyk (Container Security)

Remote VM (for deployment)

Ngrok (used earlier for webhook setup)

# Lessons Learned
- The importance of scanning images before pushing

- Proper use of Jenkins Credentials Manager.

- Automating end-to-end pipelines builds both skill and speed.

- Avoid storing private keys, ip addr or secrets in scripts and in Jenkins.

