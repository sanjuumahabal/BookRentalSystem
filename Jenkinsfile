pipeline {
    agent any

    environment {
        DOCKER_HOST = "tcp://localhost:2375"
        DOCKER_IMAGE = "your-dockerhub-username/django-app"
    }

    stages {
        // STAGE 1: Checkout from GitHub
        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/sanjuumahabal/BookRentalSystem.git',
                credentialsId: 'github-credentials'
            }
        }

        // STAGE 2: Continuous Integration (CI)
        stage('Install Dependencies') {
            steps {
                bat 'python -m venv venv'
                bat 'call venv\\Scripts\\activate && pip install -r requirements.txt'
            }
        }

        // STAGE 3: Continuous Testing (CT)
        stage('Run Tests') {
            steps {
                bat 'call venv\\Scripts\\activate && python manage.py test'
            }
        }

        // STAGE 4: Build Docker Image
        stage('Docker Build') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:latest")
                }
            }
        }

        // STAGE 5: Continuous Deployment (CD)
        stage('Deploy') {
            steps {
                script {
                    // Stop and remove old container
                    bat 'docker stop django-app || true'
                    bat 'docker rm django-app || true'
                    
                    // Run new container
                    docker.image("${DOCKER_IMAGE}:latest").run(
                        '-p 8000:8000 --name django-app -d'
                    )
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
