pipeline {
    agent any

    tools {
        maven 'Maven' // Doit correspondre au nom configuré dans Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE = 'SonarQube-10' // Doit être exactement le nom défini dans Manage Jenkins > Configure System
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hasnahatti70/ReactJS-Spring-Boot-CRUD-Full-Stack-App'
            }
        }

        stage('Build') {
            steps {
                dir('springboot-backend') {
                    sh 'mvn clean verify'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('springboot-backend') {
                    withSonarQubeEnv("${SONARQUBE}") {
                        sh 'mvn sonar:sonar -Dsonar.projectKey=springcrud'
                    }
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline exécutée avec succès, analyse SonarQube OK."
        }
        failure {
            echo "❌ Échec de la pipeline. Vérifiez les logs (SonarQube ou Quality Gate)."
        }
    }
}
