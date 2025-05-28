pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        SONARQUBE = 'SonarQube-10'
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
                    if ! command -v gitleaks &> /dev/null; then
                        echo "🔍 Installation de Gitleaks..."
                        curl -sSL https://github.com/gitleaks/gitleaks/releases/latest/download/gitleaks-linux-amd64 -o gitleaks
                        chmod +x gitleaks
                        sudo mv gitleaks /usr/local/bin/
                    fi
                    echo "🔍 Exécution de Gitleaks..."
                    gitleaks detect --source=. --no-git --report-format=json --report-path=gitleaks-report.json
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
