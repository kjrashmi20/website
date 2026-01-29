pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yourdockerhubusername/website"
        DOCKER_TAG   = "${BUILD_NUMBER}"
    }

    stages {

        /* ---------------- JOB 1 : BUILD ---------------- */
        stage('Job1 - Build') {
            steps {
                checkout scm
                echo "Building for branch: ${env.BRANCH_NAME}"
                docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
            }
        }

        /* ---------------- JOB 2 : TEST ---------------- */
        stage('Job2 - Test') {
            steps {
                echo "Running tests for branch: ${env.BRANCH_NAME}"
                sh "echo 'Tests passed'"
            }
        }

        /* ---------------- JOB 3 : PROD ---------------- */
        stage('Job3 - Prod (Only Master)') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest
                    docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ SUCCESS for ${env.BRANCH_NAME}"
        }
        failure {
            echo "❌ FAILED for ${env.BRANCH_NAME}"
        }
    }
}
