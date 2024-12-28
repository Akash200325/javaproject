pipeline {
    agent any

    environment {
        // Load SonarQube token from Jenkins credentials
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Pull the code from your Git repository
                git branch: 'main',
                    url: 'https://github.com/Akash200325/javaproject'
            }
        }

        stage('Build') {
            steps {
                // Clean and build the project
                sh 'mvn clean install'
            }
        }

        stage('Run Tests') {
            steps {
                // Run unit tests
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Run SonarQube analysis
                sh '''
                mvn sonar:sonar \
                    -Dsonar.projectKey=mavenworldline \
                    -Dsonar.projectName="mavenworldline" \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.login=${SONAR_TOKEN}
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
