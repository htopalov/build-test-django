pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'git_as_vault', url: 'https://github.com/htopalov/build-test-django.git']]])
            }
        }
        stage('Check Credentials') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'git_as_vault', 
                    usernameVariable: 'USER', 
                    passwordVariable: 'PASS'
                )]) {
                    sh '''
                        echo "=== Credentials Check ==="
                        printf "Username: %s\\n" "$(echo $USER | sed 's/./*/g')"
                        REAL_USER=$(echo $USER | sed 's/./*/g')
                        if [ "$USER" != "$REAL_USER" ]; then
                            echo "Username was updated!"
                        else
                            echo "Username is unchanged"
                        fi
                        
                        printf "Password: %s\\n" "$(echo $PASS | sed 's/./*/g')"
                        if [ "$PASS" != "123" ]; then
                            echo "Password was updated!"
                        else
                            echo "Password is unchanged"
                        fi
                    '''
                }
            }
        }
    }
}
