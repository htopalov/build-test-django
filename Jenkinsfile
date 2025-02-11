pipeline {
    agent any

    environment {
        // Set the Python environment
        VIRTUAL_ENV = "${WORKSPACE}/venv"
        PATH = "${VIRTUAL_ENV}/bin:${env.PATH}:/usr/local/bin"
        VAULT_CREDS = credentials('git_as_vault')
    }

    stages {
        stage('Retrieve and Use Credentials') {
            steps {
                script {
                    // Access the username and password (for DEBUGGING ONLY - REMOVE THIS LATER)
                    def username = VAULT_CREDS.username
                    def password = VAULT_CREDS.password

                    // *** DANGER: DO NOT DO THIS IN PRODUCTION ***
                    echo "Username (DEBUG ONLY - REMOVE): ${username}"
                    echo "Password (DEBUG ONLY - REMOVE): ${password}"

                    // Instead of printing, use the credentials directly:
                    withCredentials([usernamePassword(credentialsId: 'your-vault-credential-id', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                            echo "Using USERNAME: $USERNAME"
                            echo "Using PASSWORD: $PASSWORD"
                            // Use $USERNAME and $PASSWORD to connect to your system.
                            // Example: curl -u "$USERNAME:$PASSWORD" your-url
                        '''
                    }

                    // *** VERY IMPORTANT: Remove the echo statements above immediately after debugging! ***
                }
            }
        }
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    // Create a virtual environment
                    sh 'python3 -m venv venv'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install project dependencies
                    sh 'pip3 install -r requirements.txt'
                }
            }
        }

        stage('Run Migrations') {
            steps {
                script {
                    // Run Django migrations
                    sh 'python3 manage.py migrate'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run Django tests
                    sh 'python3 manage.py test'
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/test-reports/*.xml', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
