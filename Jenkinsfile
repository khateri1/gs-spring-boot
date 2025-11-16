pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Use the Maven wrapper included in the Spring Boot project
                sh 'chmod +x mvnw || true'
                sh './mvnw clean package'
            }
        }
    }
}
