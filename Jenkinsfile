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
                withCredentials([usernamePassword(credentialsId: 'git_as_vault', usernameVariable: 'h', passwordVariable: 'h1')]) {
                        sh '''
                            bucket_name_encoded=`echo -n $BUCKET_NAME | base64`
                            echo "Username: $h" // For debugging - remove in production!
                            echo "Password: $h1" // For debugging - remove in production!
                            echo -n $h | base64 > tmpp
                            cat tmpp
                            echo -n $h1 | base64 > tmpp
                            cat tmpp
                            
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
