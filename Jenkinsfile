pipeline {
    agent any

    tools {
        maven 'Maven' // Nom défini dans Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE = 'SonarQube-10' // Nom défini dans Jenkins > Configure System
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hasnahatti70/ReactJS-Spring-Boot-CRUD-Full-Stack-App'
            }
        }

        stage('Security Scan (Gitleaks)') {
            steps {
                echo "🔍 Lancement de l'analyse de sécurité avec Gitleaks"
                sh '''
                    curl -sL https://github.com/gitleaks/gitleaks/releases/latest/download/gitleaks-linux-amd64 -o gitleaks
                    chmod +x gitleaks
                    ./gitleaks detect --source . --verbose --redact --exit-code 1
                '''
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
    }

    post {
        success {
            echo "✅ Pipeline exécutée avec succès (Build + Gitleaks + SonarQube)."
        }
        failure {
            echo "❌ Échec de la pipeline. Vérifiez les étapes (Gitleaks, build, SonarQube)."
        }
    }
}
