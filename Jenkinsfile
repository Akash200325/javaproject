pipeline {
    agent any

    environment {
        // SonarQube server settings
        SONARQUBE_URL = 'http://localhost:9000'
        
        // Global token for SonarQube authentication
        GLOBAL_SONAR_TOKEN = credentials('sonar-token') // Replace with the global token ID in Jenkins
        
        // Project-specific token (hardcoded for this example)
        PROJECT_SONAR_TOKEN = 'sqp_c2d9b85b64d0d7a94a1baafd173d3b38c3efc624' // Replace with your project token
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Akash200325/javaproject'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') { // Refers to the global SonarQube server configuration in Jenkins
                        sh """
                        mvn sonar:sonar \
                            -Dsonar.projectKey=mavenjenkines \
                            -Dsonar.projectName='mavenjenkines' \
                            -Dsonar.host.url=${SONARQUBE_URL} \
                            -Dsonar.login=${PROJECT_SONAR_TOKEN} // Use project-specific token for analysis
                        """
                    }
                }
            }
        }
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
    }
}
