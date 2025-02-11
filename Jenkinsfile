pipeline {
    agent any
    
    stages {
        stage('Check Credentials') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'your-credential-id', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        echo "Credentials check:" > /tmp/creds.txt
                        echo $USER >> /tmp/creds.txt
                        echo $PASS >> /tmp/creds.txt
                        cat /tmp/creds.txt
                        rm /tmp/creds.txt
                    '''
                }
            }
        }
    }
}
