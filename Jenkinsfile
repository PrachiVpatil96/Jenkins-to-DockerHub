pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'devlop', url: 'https://github.com/PrachiVpatil96/Jenkins-To-Dockerhub.git'
            }
        }

        stage("Docker Build & Push") {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerHub') {   
                        sh "docker build --build-arg TMDB_V3_API_KEY=80be8bf15c0abc1246b10cfc43b9e8f6 -t nnetflix ."
                        sh "docker tag nnetflix prachiii123/nnetflix:1.1"
                        sh "docker push prachiii123/nnetflix:1.1"
                    }
                }
            }
        }
        
        stage("Run Docker Container") {
            steps {
                script {
                    sh "docker container  run -d -p 9000:80 prachiii123/nnetflix:1.1"
                    
                }
            }
        }
    }
}
