pipeline {
    agent any

    tools {
        maven 'Maven' // Configuré dans Manage Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE = 'SonarQube-10' // Configuré dans Manage Jenkins > Configure System
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/hasnahatti70/ReactJS-Spring-Boot-CRUD-Full-Stack-App'
            }
        }

        stage('Gitleaks Scan') {
            steps {
                bat '''
                echo 🔍 Utilisation de Gitleaks local installé...
                "C:\\Users\\MTechno\\Downloads\\gitleaks_8.26.0_windows_x64\\gitleaks.exe" detect --source=. --verbose --report-format=json --report-path=gitleaks-report.json || exit /b 0

                echo 📄 Résultats du scan Gitleaks :
                type gitleaks-report.json || echo ⚠️ Aucun résultat trouvé.
                '''
            }
        }

        stage('Build') {
            steps {
                dir('springboot-backend') {
                    bat 'mvn clean verify'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                dir('springboot-backend') {
                    withSonarQubeEnv("${SONARQUBE}") {
                        bat 'mvn sonar:sonar -Dsonar.projectKey=springcrud'
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
