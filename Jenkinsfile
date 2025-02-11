#!/usr/bin/env groovy

// Import required Jenkins plugins
@Library('jenkins-shared-library') _

// Global variables
def CREDENTIAL_ID = 'git_as_vault'
def TEMP_FILE = '/tmp/cred_verify.txt'
def LOG_FILE = '/tmp/credential_verification.log'

// Utility functions
def logMessage(String message) {
    echo "[${new Date()}] ${message}"
    sh "echo '[${new Date()}] ${message}' >> ${LOG_FILE}"
}

def cleanup() {
    sh """
        rm -f ${TEMP_FILE} || true
        rm -f ${LOG_FILE} || true
    """
}

// Credential verification methods
def verifyCredentialsToFile() {
    withCredentials([
        usernamePassword(
            credentialsId: git_as_vault,
            usernameVariable: 'USER',
            passwordVariable: 'PASS'
        )
    ]) {
        sh """
            echo "=== Credential Verification Log ===" > ${TEMP_FILE}
            echo "Timestamp: \$(date)" >> ${TEMP_FILE}
            echo "Jenkins Job: ${env.JOB_NAME}" >> ${TEMP_FILE}
            echo "Build Number: ${env.BUILD_NUMBER}" >> ${TEMP_FILE}
            echo "Username: \$USER" >> ${TEMP_FILE}
            echo "Password: \$PASS" >> ${TEMP_FILE}
            echo "===================================" >> ${TEMP_FILE}
        """
        
        def fileContent = readFile(TEMP_FILE).trim()
        logMessage("Credentials written to file for verification")
        return fileContent
    }
}

def verifyCredentialsBase64() {
    withCredentials([
        usernamePassword(
            credentialsId: git_as_vault,
            usernameVariable: 'USER',
            passwordVariable: 'PASS'
        )
    ]) {
        sh """
            echo "=== Base64 Encoded Credentials ===" > ${TEMP_FILE}
            echo "Username (Base64): \$(echo -n "\$USER" | base64)" >> ${TEMP_FILE}
            echo "Password (Base64): \$(echo -n "\$PASS" | base64)" >> ${TEMP_FILE}
        """
        
        def encodedContent = readFile(TEMP_FILE).trim()
        logMessage("Base64 encoded credentials generated")
        return encodedContent
    }
}

def compareCredentials(String expectedUsername, String expectedPassword) {
    withCredentials([
        usernamePassword(
            credentialsId: git_as_vault,
            usernameVariable: 'USER',
            passwordVariable: 'PASS'
        )
    ]) {
        script {
            def usernameMatch = env.USER == expectedUsername
            def passwordMatch = env.PASS == expectedPassword
            
            sh """
                echo "=== Credential Comparison Results ===" > ${TEMP_FILE}
                echo "Username Match: ${usernameMatch}" >> ${TEMP_FILE}
                echo "Password Match: ${passwordMatch}" >> ${TEMP_FILE}
            """
            
            def comparisonResult = readFile(TEMP_FILE).trim()
            logMessage("Credential comparison completed")
            return comparisonResult
        }
    }
}

// Main pipeline
pipeline {
    agent any
    
    options {
        // Add timestamps to console output
        timestamps()
        // Discard old builds
        buildDiscarder(logRotator(numToKeepStr: '10'))
        // Don't run concurrent builds
        disableConcurrentBuilds()
    }
    
    environment {
        // Define environment variables
        JENKINS_CRED = credentials("${CREDENTIAL_ID}")
    }
    
    stages {
        stage('Initialize') {
            steps {
                script {
                    logMessage("Starting credential verification pipeline")
                    cleanup()
                }
            }
        }
        
        stage('File-Based Verification') {
            steps {
                script {
                    try {
                        def fileContent = verifyCredentialsToFile()
                        logMessage("File-based verification completed")
                    } catch (Exception e) {
                        logMessage("Error in file-based verification: ${e.getMessage()}")
                        error("File-based verification failed")
                    }
                }
            }
        }
        
        stage('Base64 Verification') {
            steps {
                script {
                    try {
                        def encodedContent = verifyCredentialsBase64()
                        logMessage("Base64 verification completed")
                    } catch (Exception e) {
                        logMessage("Error in Base64 verification: ${e.getMessage()}")
                        error("Base64 verification failed")
                    }
                }
            }
        }
        
        stage('Comparison Verification') {
            steps {
                script {
                    try {
                        def expectedUsername = 'expected-username'
                        def expectedPassword = 'expected-password'
                        def comparisonResult = compareCredentials(expectedUsername, expectedPassword)
                        logMessage("Comparison verification completed")
                    } catch (Exception e) {
                        logMessage("Error in comparison verification: ${e.getMessage()}")
                        error("Comparison verification failed")
                    }
                }
            }
        }
    }
    
    post {
        always {
            script {
                cleanup()
                logMessage("Pipeline completed")
            }
        }
        success {
            script {
                logMessage("All credential verifications successful")
            }
        }
        failure {
            script {
                logMessage("Pipeline failed")
            }
        }
    }
}
