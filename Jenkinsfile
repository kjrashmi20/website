pipeline {
    agent any

    environment {
        IMAGE_NAME = "abode-webapp"
        CONTAINER_NAME = "abode-prod"
    }

    stages {
        /* Runs on BOTH develop and master */
        stage('Job1 - Build') {
            when {
                anyOf {
                    branch 'develop'
                    branch 'master'
                }
            }
            steps {
                echo "Building Docker image"
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        /* Runs on BOTH develop and master */
        stage('Job2 - Test') {
            when {
                anyOf {
                    branch 'develop'
                    branch 'master'
                }
            }
            steps {
                echo "Testing application inside container"
                sh '''
                docker rm -f test-container || true
                docker run -d --name test-container -p 8081:80 $IMAGE_NAME
                sleep 10
                curl -f http://localhost:8081
                docker rm -f test-container
                '''
            }
        }

        /* Runs ONLY on master */
        stage('Job3 - Prod') {
            when {
                branch 'master'
            }
            steps {
                echo "Deploying to production"
                sh '''
                docker rm -f $CONTAINER_NAME || true
                docker run -d --name $CONTAINER_NAME -p 80:80 $IMAGE_NAME
                '''
            }
        }
    }

    post {
        always {
            echo "Pipeline completed"
        }
        failure {
            echo "Pipeline failed"
        }
    }
}
