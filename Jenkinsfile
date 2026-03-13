// pipeline {
//     agent any

//     stages {

//         // stage('Clone Repository') {
//         //     steps {
//         //         git 'https://github.com/gunasekarU/react-cicd.git'
//         //     }
//         // }

//         stage('Install Dependencies') {
//             steps {
//                 bat 'npm install'
//             }
//         }

//         stage('Build Project') {
//             steps {
//                 bat 'npm run build'
//             }
//         }

//      stage('Build Docker Image') {
//             steps {
//                 bat "docker build -t gunasekar2066/react-cicd:${BUILD_NUMBER} ."
//             }
//         }

//         // stage('Push Docker Image') {
//         //     steps {
//         //         bat "docker push gunasekar2066/react-cicd:${BUILD_NUMBER}"
//         //     }
//         // }

//         stage('Push Docker Image') {
//                 steps {
//                     withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
//                         bat 'docker login -u %USER% -p %PASS%'
//                         bat "docker push gunasekar2066/react-cicd:${BUILD_NUMBER}"
//                     }
//                 }
//             }

//         // stage('Deploy Kubernetes') {
//         //     steps {
//         //         bat "kubectl set image deployment/react-app react-container=gunasekar2066/react-cicd:${BUILD_NUMBER}"
//         //     }
//         // }
//         // stage('Deploy Kubernetes') {
//         //     steps {
//         //         bat 'set KUBECONFIG=C:\\Users\\2415\\.kube\\config'
//         //         bat 'kubectl set image deployment/react-app react-container=gunasekar2066/react-cicd:%BUILD_NUMBER%'
//         //     }
//         // }
//         stage('Start Kubernetes') {
//             steps {
//                 bat 'minikube start'
//             }
//         }

//         stage('Deploy Kubernetes') {
//             steps {
//                 bat 'kubectl --context=minikube set image deployment/react-app react-container=gunasekar2066/react-cicd:%BUILD_NUMBER%'
//             }
//         }
//     }
// }


// pipeline {
//     agent any

//     environment {
//         DOCKER_IMAGE = "gunasekar2066/react-cicd"
//         DOCKER_TAG = "${BUILD_NUMBER}"
//     }

//     stages {

//         stage('Install Dependencies') {
//             steps {
//                 // Install npm dependencies
//                 bat 'npm install'
//             }
//         }

//         stage('Build React Project') {
//             steps {
//                 // Create production build
//                 bat 'npm run build'
//             }
//         }

//         stage('Build Docker Image') {
//             steps {
//                 // Build Docker image with tag as BUILD_NUMBER
//                 bat "docker build -t %DOCKER_IMAGE%:%DOCKER_TAG% ."
//             }
//         }

//         stage('Login to Docker Hub') {
//             steps {
//                 // Use Jenkins stored credentials
//                 withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
//                     bat 'docker login -u %USER% -p %PASS%'
//                 }
//             }
//         }

//         stage('Push Docker Image') {
//             steps {
//                 // Push image to Docker Hub
//                 bat "docker push %DOCKER_IMAGE%:%DOCKER_TAG%"
//             }
//         }

//         stage('Deploy to Kubernetes') {
//             steps {
//                 // Make sure minikube/Docker Desktop Kubernetes is running manually
//                 // Deploy new image to your deployment
//                 bat "kubectl --context=minikube set image deployment/react-app react-container=%DOCKER_IMAGE%:%DOCKER_TAG%"
//             }
//         }
//     }

//     post {
//         always {
//             echo "Build #${BUILD_NUMBER} finished"
//         }
//     }
// }



pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "gunasekar2066/react-cicd"
        DOCKER_TAG = "${BUILD_NUMBER}"
        // Set kubeconfig path for Jenkins
        KUBECONFIG = "C:\\ProgramData\\Jenkins\\.kube\\config"
    }

    stages {

        stage('Install Dependencies') {
            steps {
                echo "Installing npm dependencies..."
                bat 'npm install'
            }
        }

        stage('Build React Project') {
            steps {
                echo "Building React project..."
                bat 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image..."
                bat "docker build -t %DOCKER_IMAGE%:%DOCKER_TAG% ."
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo "Logging in to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat 'docker login -u %USER% -p %PASS%'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Pushing Docker image..."
                bat "docker push %DOCKER_IMAGE%:%DOCKER_TAG%"
            }
        }

        stage('Test Kubernetes Connection') {
            steps {
                echo "Testing kubectl connection..."
                // This ensures Jenkins can reach the Minikube cluster
                bat 'kubectl get nodes'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Updating Kubernetes deployment with new image..."
                // Ensure the deployment exists; otherwise apply deployment.yaml
                bat "kubectl apply -f %WORKSPACE%\\deployment.yaml"
                bat "kubectl --context=minikube set image deployment/react-app react-container=%DOCKER_IMAGE%:%DOCKER_TAG%"
            }
        }

        stage('Expose Service') {
            steps {
                echo "Exposing React app service..."
                // Optional: create service if not exists
                bat '''
                kubectl get svc react-app || kubectl expose deployment react-app --type=NodePort --name=react-app --port=3000 --target-port=3000
                '''
                // Get NodePort to access app
                bat 'kubectl get svc react-app'
            }
        }

    }

    post {
        always {
            echo "Build #${BUILD_NUMBER} finished"
        }
    }
}