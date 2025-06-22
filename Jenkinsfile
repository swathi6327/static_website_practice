pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'SonarQube'                    
        NEXUS_REPO_URL = 'http://3.7.254.85:30801/#browse/search/maven/'
        NEXUS_CREDENTIALS_ID = 'nexus-creds'           
    }
    tools {
    maven 'maven3'
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                git(
                 branch: "main", url: "https://github.com/swathi6327/static_website_practice.git",
                 credentialsId: 'GIT-CRED'
                )    
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }
      
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Manual Approval for Deployment') {
            steps {
                input message: "Approve to deploy artifact to Nexus repository?", ok: "Deploy"
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${nexus-creds}",
                    usernameVariable: 'admin',
                    passwordVariable: 'admin'
                )]) {
                    sh """
                        curl -u $NEXUS_USER:$NEXUS_PASS --upload-file target/your-artifact.jar \
                        ${NEXUS_REPO_URL}/com/yourorg/your-artifact/1.0/your-artifact-1.0.jar
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
