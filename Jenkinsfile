pipeline {
  agent any
  stages {
    stage('Checkout') {
      when {
        anyOf {
          branch 'master'
          branch 'develop'
          branch 'staging'
        }

      }
      steps {
        echo 'Checking out from Git'
        checkout scm
      }
    }
    stage('Code Review') {
            parallel {
                stage('Code review') {
                    environment {
                        scannerHome = tool 'sonarQubeScanner'
                    }
                    when {
                        branch 'develop'
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

   stage('Approval Code Review') {
      when {
        branch 'develop'
      }
      steps {
        input 'Approval Code Review'
      }
    }

    stage('Approval Staging') {
      when {
        branch 'staging'
      }
      steps {
        input 'Approval Staging'
      }
    }

    stage('build') {
      when {
        branch 'master'
      }
      steps {
        echo 'build docker'
      }
    }

    stage('nexus') {
      when {
        branch 'master'
      }
      steps {
        echo 'post build to nexus'
      }
    }

    stage('Approval') {
      when {
        branch 'master'
      }
      steps {
        input 'deploy to argocd'
      }
    }

    stage('deploy to argocd') {
      when {
        branch 'master'
      }
      steps {
        echo 'deploy to argocd'
      }
    }

  }
  environment {
    SERVICE = 'testing-golang'
  }
}
