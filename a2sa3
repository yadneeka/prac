pipeline {
    agent { label 'builtin' }

    environment {
        CONTAINER_NAME = "some-postgres"
        IMAGE = "postgres:latest"
    }

    stages {

        stage('Pull PostgreSQL Image') {
            steps {
                echo "📦 Pulling official PostgreSQL image..."
                sh 'docker pull ${IMAGE}'
            }
        }

        stage('Run PostgreSQL Container Securely') {
            steps {
                echo "🔒 Starting PostgreSQL container securely..."
                withCredentials([string(credentialsId: 'postgres-password', variable: 'PG_PASS')]) {
                    sh '''
                        # Remove if already running
                        docker rm -f ${CONTAINER_NAME} || true

                        # Run new container with password injected from Jenkins credentials
                        docker run -d \
                            --name ${CONTAINER_NAME} \
                            -e POSTGRES_PASSWORD=${PG_PASS} \
                            -p 5432:5432 \
                            ${IMAGE}
                    '''
                }
            }
        }

        stage('Verify Running Container') {
            steps {
                sh '''
                    echo "✅ Listing running containers:"
                    docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Image}}\t{{.Status}}"
                '''
            }
        }
    }

    post {
        success {
            echo "🎯 PostgreSQL container launched successfully and is running in detached mode."
        }
        failure {
            echo "❌ Build failed while creating PostgreSQL container."
        }
        always {
            echo "Pipeline finished."
        }
    }
}
