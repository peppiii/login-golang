pipeline {
    agent {
        node {
            label 'master'
        }
    }
    environment {
        SERVICE = 'testing-golang'
    }
    stages {
        stage('Checkout') {
            when {
                anyOf { branch 'master'; }
            }
            steps {
                echo 'Checking out from Git'
                checkout scm
            }
        }
        stage('Build') {
            parallel {
                stage('Code review') {
                    environment {
                        scannerHome = tool 'sonarQubeScanner'
                    }
                    when {
                        branch 'master'
                    }
                    steps {
                        withSonarQubeEnv('sonarQube') {
                            sh "${scannerHome}/bin/sonar-scanner"
                        }
                        timeout(time: 5, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                    }
                }
                    post {
                       success {
                           slackSend color: '#00FF00', message: "Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} SUCCESS (<${env.BUILD_URL}|Open>)"
                       }
                       failure {
                           slackSend color: '#FF0000', message: "Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} FAILED (<${env.BUILD_URL}|Open>)"
                       }
                    }
                }
            }
        }
    }
