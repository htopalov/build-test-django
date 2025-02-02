pipeline {
    agent any

    environment {
        // Set the Python environment
        VIRTUAL_ENV = "${WORKSPACE}/venv"
        PATH = "${VIRTUAL_ENV}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Python and Pip') {
            steps {
                script {
                    // Install Python and pip if not already installed
                    sh '''
                    if ! command -v python3 &> /dev/null; then
                        echo "Python3 not found, installing..."
                        sudo yum update -y
                        sudo yum install -y python3
                    fi
                    if ! command -v pip3 &> /dev/null; then
                        echo "Pip3 not found, installing..."
                        sudo yum install -y python3-pip
                    fi
                    '''
                }
            }
        }

        stage('Setup Python Environment') {
            steps {
                script {
                    // Install virtualenv if not already installed
                    sh 'pip3 install virtualenv'
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


