pipeline {
    agent any

    environment {
        // Nexus running in Docker on your Mac
        NEXUS_URL            = 'http://host.docker.internal:8081'
        NEXUS_REPO           = 'helloworld-raw-repo'
        NEXUS_CREDENTIALS_ID = 'nexus-admin'
    }

    stages {
        stage('Build') {
            steps {
                // Your Spring Boot project is inside the "complete" folder
                dir('complete') {
                    sh 'chmod +x mvnw'
                    sh './mvnw clean package'
                }
            }
        }

        stage('Upload to Nexus') {
            steps {
                dir('complete') {
                    script {
                        // Find the jar that was just built
                        def jar = sh(
                            script: "ls target/*.jar",
                            returnStdout: true
                        ).trim()

                        // Use Jenkins credentials to talk to Nexus
                        withCredentials([
                            usernamePassword(
                                credentialsId: NEXUS_CREDENTIALS_ID,
                                usernameVariable: 'NUSER',
                                passwordVariable: 'NPASS'
                            )
                        ]) {
                            sh """
                                curl -v -u ${NUSER}:${NPASS} \
                                  --upload-file ${jar} \
                                  ${NEXUS_URL}/repository/${NEXUS_REPO}/\$(basename ${jar})
                            """
                        }
                    }
                }
            }
        }
    }
}
