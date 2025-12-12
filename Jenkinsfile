pipeline {
    agent any
    tools {
        jdk 'java21'
        nodejs 'nodejs16'
    }

    environment {
        SCANNER_HOME = tool 'sonarqube-scanner'
    }

    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }

        stage('checkout from git') {
            steps {
                git branch: 'master', url: 'https://github.com/alakhdar1492003/end-to-end-CI-CD-project.git' 
            }
        }

        stage('sonarqube analysis') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=sonar-project \
                        -Dsonar.projectName=sonar-project \
                        -Dsonar.sources=.
                    """
                }
            }
        }

        stage('quality gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialId: 'sonarqube-token'
                }
            }
        }

        stage('install dependences') {
            steps {
                sh 'npm install'
            }
        }

        stage('trivy fs scan') {
            steps {
                sh 'trivy fs . > trivyfs.txt'
            }
        }
    }
}
