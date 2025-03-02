# **Automated Docker Image Build & Push with Jenkins** 🚀

## **Project Overview**
This project sets up a **Jenkins CI/CD pipeline** to **automate the building and pushing of Docker images** to Docker Hub.

## **Prerequisites**
Ensure you have the following installed on your **Jenkins server**:

- **Jenkins** (latest version)
- **Docker**
- **Git**
- A **Docker Hub account**
- A **GitHub repository** for the project

---

## **Step 1: Install Docker on Jenkins Server**
Run the following commands on your **Jenkins server**:
```sh
sudo apt update && sudo apt install docker.io -y
sudo usermod -aG docker jenkins  # Allow Jenkins to use Docker
sudo systemctl restart docker
```

---

## **Step 2: Configure Docker Hub Credentials in Jenkins**
1. Go to **Jenkins Dashboard** → **Manage Jenkins** → **Manage Credentials**
2. Under **Global Credentials**, click **Add Credentials**
3. Select **Username and Password** and enter:
   - **Username:** *Your Docker Hub username*
   - **Password:** *Your Docker Hub password*
4. Set **ID** as `dockerhub-credentials` and save.

---

## **Step 3: Create a Jenkins Pipeline (Jenkinsfile)**
Inside your **GitHub repository**, create a file named `Jenkinsfile` with the following content:

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "your-dockerhub-username/your-image-name"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-repo.git'  // Replace with your actual repo
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }
}
```

---

## **Step 4: Create a Jenkins Job**
1. Go to **Jenkins Dashboard** → Click **New Item** → Select **Pipeline**
2. Under **Pipeline Definition**, choose **Pipeline script from SCM**
3. Set **SCM** to **Git** and enter your **GitHub repo URL**
4. Click **Save**, then **Build Now**

---

## **Step 5: Verify the Docker Image on Docker Hub**
Once the pipeline runs successfully:
1. Go to **Docker Hub** → Navigate to your repository
2. You should see the newly pushed Docker image 🎉

---

## **Bonus: Tagging the Image with Git Commit SHA**
Modify the **Push Image** stage in `Jenkinsfile` to tag images with the Git commit SHA:

```groovy
sh "docker tag ${DOCKER_IMAGE}:latest ${DOCKER_IMAGE}:${GIT_COMMIT}"
sh "docker push ${DOCKER_IMAGE}:${GIT_COMMIT}"
```

---

## **Conclusion**
This project demonstrates how to automate Docker image **builds and pushes** using **Jenkins pipelines**, improving **DevOps workflows**! 🚀

Would you like to extend this project by deploying the image to **Kubernetes**? Let me know! 😊
