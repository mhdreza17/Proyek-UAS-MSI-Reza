pipeline {
    agent any
    environment {
        SONAR_TOKEN = credentials('sonarqube-token')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Prepare') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'echo Preparing environment on Unix agent'
                    } else {
                        bat 'echo Preparing environment on Windows agent'
                    }
                }
            }
        }

        stage('Backend Static Checks') {
            when { expression { fileExists('backend') } }
            steps {
                dir('backend') {
                    script {
                        if (isUnix()) {
                            sh 'python -m pip install --user -r Requirements.txt || true'
                            sh 'python -m pip install --user flake8 || true'
                            sh 'flake8 || true'
                        } else {
                            bat 'python -m pip install --user -r Requirements.txt || exit 0'
                            bat 'python -m pip install --user flake8 || exit 0'
                            bat 'flake8 || exit 0'
                        }
                    }
                }
            }
        }

        stage('Frontend Static Checks') {
            when { expression { fileExists('frontend') } }
            steps {
                dir('frontend') {
                    script {
                        if (isUnix()) {
                            sh 'flutter pub get || true'
                            sh 'flutter analyze || true'
                        } else {
                            bat 'flutter pub get || exit 0'
                            bat 'flutter analyze || exit 0'
                        }
                    }
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    script {
                        if (isUnix()) {
                            sh 'sonar-scanner -Dsonar.login=${SONAR_TOKEN}'
                        } else {
                            bat 'D:\\sonar-scanner\\bin\\sonar-scanner.bat -Dsonar.login=%SONAR_TOKEN%'
                        }
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
