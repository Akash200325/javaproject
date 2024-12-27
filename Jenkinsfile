pipeline {
    agent any

    tools {
        jdk 'jdk11'                // Use JDK 11 or the version you need
        sonar 'sonar-scanner'       // SonarQube Scanner name configured in Jenkins
    }

    environment {
        SONARQUBE_SERVER = 'sonar-cube'  // The name of your SonarQube server in Jenkins
        SONARQUBE_TOKEN = credentials('sonar-token') // Reference the SonarQube token stored in Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Checkout the source code from the repository
            }
        }

        stage('Build') {
            steps {
                // Run the Maven build command
                sh 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-cube') {  // Use SonarQube environment configured in Jenkins
                    // Run the SonarQube analysis with the token
                    sh """
                        mvn clean verify sonar:sonar \
                          -Dsonar.projectKey=mavenjenkines \
                          -Dsonar.projectName='mavenjenkines' \
                          -Dsonar.host.url=http://localhost:9000 \
                          -Dsonar.login=$SONARQUBE_TOKEN
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                // Wait for the SonarQube Quality Gate to pass
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        always {
            // Clean workspace after build
            cleanWs()
        }

        failure {
            echo 'Build failed!'
        }

        success {
            echo 'Build and SonarQube analysis successful!'
        }
    }
}
