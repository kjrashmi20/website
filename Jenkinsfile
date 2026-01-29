pipeline {
    agent any

    environment {
        IMAGE_NAME = "abode-webapp"
        CONTAINER_NAME = "abode-prod"
    }

    stages {
        stage('Job1 - Build') {
            when { 
                anyOf { branch 'develop'; branch 'master' } 
            }
            steps {
                echo "Building Docker image"
                // Using standard shell is safer and avoids the "script block" error
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Job2 - Test') {
            when { 
                anyOf { branch 'develop'; branch 'master' } 
            }
            steps {
                echo "Testing application"
                sh '''
                docker rm -f test-container || true
                docker run -d --name test-container -p 8081:80 $IMAGE_NAME
                sleep 10
                curl -f http://localhost:8081
                docker rm -f test-container
                '''
            }
        }

        stage('Job3 - Prod') {
            when { branch 'master' }
            steps {
                echo "Deploying to production"
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME -p 80:80 $IMAGE_NAME
                '''
            }
        }
    }
}
