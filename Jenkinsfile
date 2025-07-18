pipeline {
    agent any

    environment {
        EC2_USER = 'ec2-user'                             // or 'ubuntu' for Ubuntu EC2
        EC2_HOST = '65.2.82.33'
        SSH_KEY = '/var/lib/jenkins/website.pem'        // Jenkins-readable path to your PEM file
        REMOTE_DIR = '/var/www/html'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "Repository auto-cloned by Jenkins"
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh '''
                echo "Deploying static site to EC2..."

                scp -i $SSH_KEY -o StrictHostKeyChecking=no -r startbootstrap-agency-gh-pages/* $EC2_USER@$EC2_HOST:$REMOTE_DIR

                echo "Deployment complete!"
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Static website deployed successfully!"
        }
        failure {
            echo "❌ Deployment failed. Check console output."
        }
    }
}
