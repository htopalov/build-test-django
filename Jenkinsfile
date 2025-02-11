pipeline {
    agent any

    environment {
        // Set the Python environment
        VIRTUAL_ENV = "${WORKSPACE}/venv"
        PATH = "${VIRTUAL_ENV}/bin:${env.PATH}:/usr/local/bin"
    }

    stages {
        stage('Retrieve and Use Credentials') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'git_as_vault', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                            echo "Username (DEBUG ONLY - REMOVE): $USERNAME" // For debugging - remove in production!
                            echo "Password (DEBUG ONLY - REMOVE): $PASSWORD" // For debugging - remove in production!

                            // *** REMOVE the echo lines above IMMEDIATELY after debugging ***

                            // Now use $USERNAME and $PASSWORD in your Git commands
                            git credentialsId: 'git_as_vault', url: 'your-git-repo-url' // or however you're using git

                            // Example:
                            // sh "git clone -u '$USERNAME' -p '$PASSWORD' 'your-git-repo-url'"
                            // or if you are already in a git repo
                            // sh "git config --global url.https://$USERNAME:$PASSWORD@.insteadOf https://"
                            // sh "git pull"


                        '''
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
