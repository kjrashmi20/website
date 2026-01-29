pipeline {
    agent any

    environment {
        IMAGE_NAME = "abode-webapp"
        CONTAINER_NAME = "abode-prod"
    }

    stages {

        /* ================= JOB 1 ================= */
        stage('Job1 - Build') {
            steps {
                echo "Building Docker image"
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        /* ================= JOB 2 ================= */
        stage('Job2 - Test') {
            steps {
                echo "Testing application inside container"
                sh '''
                docker rm -f test-container || true
                docker run -d --name test-container -p 8082:80 $IMAGE_NAME
                sleep 10
                curl -f http://localhost:8082
                docker rm -f test-container
                '''
            }
        }

        /* ================= JOB 3 ================= */
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
