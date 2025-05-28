pipeline {
    agent any

    tools {
        maven 'Maven' // Configuré dans Jenkins > Global Tool Configuration
    }

    environment {
        SONARQUBE = 'SonarQube-10' // Nom du serveur SonarQube dans Jenkins > Configure System
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
                    echo "🔍 Détection de l'OS Jenkins..."
                    ARCH=$(uname -m)
                    OS=$(uname -s | tr '[:upper:]' '[:lower:]')
                    
                    if [[ "$ARCH" == "x86_64" && "$OS" == "linux" ]]; then
                        FILE_NAME="gitleaks-linux-amd64"
                    elif [[ "$ARCH" == "aarch64" && "$OS" == "linux" ]]; then
                        FILE_NAME="gitleaks-linux-arm64"
                    elif [[ "$OS" == "darwin" ]]; then
                        FILE_NAME="gitleaks-darwin-arm64"
                    else
                        echo "❌ OS ou architecture non supporté(e) par ce script automatique."
                        exit 1
                    fi

                    echo "🔍 Téléchargement de Gitleaks ($FILE_NAME)..."
                    curl -sSL https://github.com/gitleaks/gitleaks/releases/latest/download/$FILE_NAME -o gitleaks
                    chmod +x gitleaks

                    echo "🔍 Exécution du scan Gitleaks..."
                    ./gitleaks detect --source=. --no-git --report-format=json --report-path=gitleaks-report.json || true

                    echo "📄 Résultats du scan Gitleaks :"
                    cat gitleaks-report.json || echo "⚠️ Aucun résultat trouvé ou fichier manquant."
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
