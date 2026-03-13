pipeline {
    agent any

    stages {

        // stage('Clone Repository') {
        //     steps {
        //         git 'https://github.com/gunasekarU/react-cicd.git'
        //     }
        // }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build Project') {
            steps {
                bat 'npm run build'
            }
        }

     stage('Build Docker Image') {
            steps {
                bat "docker build -t gunasekar2066/react-cicd:${BUILD_NUMBER} ."
            }
        }

        stage('Push Docker Image') {
            steps {
                bat "docker push gunasekar2066/react-cicd:${BUILD_NUMBER}"
            }
        }

        stage('Deploy Kubernetes') {
            steps {
                bat "kubectl set image deployment/react-app react-container=gunasekar2066/react-cicd:${BUILD_NUMBER}"
            }
        }
    }
}