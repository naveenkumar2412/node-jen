pipeline {
    agent any
    
    environment {
        // Set environment variables
        DEPLOY_SERVER = 'ubuntu@15.206.205.249' // Replace with EC2 username and IP address
        DEPLOY_DIR = '/home/ubuntu/node-jen/app.js' // Path to your app on EC2
        NODE_VERSION = '18' // Node.js version to use
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/naveenkumar2412/node-jen.git' // Replace with your Git repository
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Skipping installation, Node.js already installed on the system.'
                
            }
        }

        stage('Build') {
            steps {
                script {
                    // If your app has a build step (e.g., TypeScript or Webpack)
                    sh 'npm run build' // Optional, remove if not needed
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    // Deploy the Node.js application to EC2
                    sshagent(['bc49d7a3-a033-47cb-8f71-594dc931d036']) { // Use the credentials ID set in Jenkins
                        sh '''
                        # Copy files to EC2
                        scp -r * ${DEPLOY_SERVER}:${DEPLOY_DIR}

                        # SSH into EC2 and restart the application (ensure PM2 or similar is installed on EC2)
                        ssh ${DEPLOY_SERVER} <<EOF
                        cd ${DEPLOY_DIR}
                        npm install
                        pm2 stop app || true  # Stop app if it's running
                        pm2 start app.js --name app  # Start app using pm2 (or your preferred process manager)
                        EOF
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
