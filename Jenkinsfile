pipeline {
    agent any

    tools {
        maven 'Maven' // Nom défini dans Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE = 'SonarQube-10' // Nom exact du serveur SonarQube défini dans Jenkins > Configure System
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hasnahatti70/ReactJS-Spring-Boot-CRUD-Full-Stack-App'
            }
        }

        stage('Gitleaks Scan') {
            steps {
                sh '''
                    echo "🔍 Téléchargement de Gitleaks localement..."
                    curl -sSL https://github.com/gitleaks/gitleaks/releases/latest/download/gitleaks-linux-amd64 -o gitleaks
                    chmod +x gitleaks

                    echo "🔍 Exécution du scan Gitleaks..."
                    ./gitleaks detect --source=. --no-git --report-format=json --report-path=gitleaks-report.json
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
            echo "✅ Pipeline exécutée avec succès, analyse SonarQube OK."
        }
        failure {
            echo "❌ Échec de la pipeline. Vérifiez les logs pour plus de détails."
        }
        always {
            archiveArtifacts artifacts: 'gitleaks-report.json', allowEmptyArchive: true
        }
    }
}
