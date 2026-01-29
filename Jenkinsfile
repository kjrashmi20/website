pipeline {
    agent any

    environment {
        IMAGE_NAME = "abode-webapp"
        CONTAINER_NAME = "abode-prod"
    }

    stages {
        stage('Job1 - Build') {
            when {
                expression { 
                    return env.GIT_BRANCH == 'develop' || env.GIT_BRANCH == 'origin/develop' || 
                           env.GIT_BRANCH == 'master'  || env.GIT_BRANCH == 'origin/master' 
                }
            }
            steps {
                echo "Building Docker image"
                sh "docker build -t ${IMAGE_NAME} ."
            }
        }

        stage('Job2 - Tests') {
            when {
                expression { 
                    return env.GIT_BRANCH == 'develop' || env.GIT_BRANCH == 'origin/develop' || 
                           env.GIT_BRANCH == 'master'  || env.GIT_BRANCH == 'origin/master' 
                }
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
            when {
                expression { 
                    return env.GIT_BRANCH == 'master' || env.GIT_BRANCH == 'origin/master' 
                }
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
}
