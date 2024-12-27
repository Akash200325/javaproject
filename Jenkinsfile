pipeline {
    agent any

    environment {
        SONARQUBE_URL = 'http://localhost:9000'          // SonarQube server running locally
        SONARQUBE_TOKEN = credentials('sonar-token')      // SonarQube token stored as Jenkins credential
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    // Run Maven build (this includes compiling and testing)
                    sh 'mvn clean verify'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarQube analysis using Maven
                    withSonarQubeEnv('SonarQube') {
                        sh '''mvn sonar:sonar \
                            -Dsonar.projectKey=mavenjenkines \
                            -Dsonar.projectName="mavenjenkines" \
                            -Dsonar.host.url=${SONARQUBE_URL} \
                            -Dsonar.login=${SONARQUBE_TOKEN}'''
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for SonarQube analysis to complete and check the Quality Gate status
                    timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate() // Ensures the build fails if the Quality Gate is not passe
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean workspace after build
        }
        success {
            echo 'Build and SonarQube analysis successful!'
        }
        failure {
            echo 'Build or SonarQube analysis failed!'
        }
    }
}