pipeline {
    agent any
    
    stages {
        stage('Check Credentials') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'git_as_vault', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        EXPECTED_USER="htopalov"
                        EXPECTED_PASS="gggggg"
                        
                        echo "=== Credentials Check ==="
                        if [ "$USER" = "$EXPECTED_USER" ]; then
                            echo "Username matches expected value"
                        else
                            echo "Username does NOT match expected value"
                        fi
                        
                        if [ "$PASS" = "$EXPECTED_PASS" ]; then
                            echo "Password matches expected value"
                        else
                            echo "Password does NOT match expected value"
                        fi
                    '''
                }
            }
        }
    }
}
