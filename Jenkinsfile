pipeline {
    agent any

    environment {
        NEXUS_URL            = 'http://localhost:8081'
        NEXUS_REPO           = 'helloworld-raw-repo'
        NEXUS_CREDENTIALS_ID = 'nexus-admin'
    }

    stages {
        stage('Build') {
            steps {
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
                        def jar = sh(
                            script: "ls target/*.jar",
                            returnStdout: true
                        ).trim()

                        withCredentials([
                            usernamePassword(
                                credentialsId: NEXUS_CREDENTIALS_ID,
                                usernameVariable: 'NUSER',
                                passwordVariable: 'NPASS'
                            )
                        ]) {
                            sh """
                                curl -u ${NUSER}:${NPASS} \
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
