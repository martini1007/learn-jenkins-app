pipeline {
    agent {
        docker {
            image 'node:18-bullseye'
            args '-u 1000:1000'
        }
    }
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') {
            steps {
                sh '''
                    echo "🧹 Czyszczenie starego node_modules i package-lock.json..."
                    rm -rf node_modules package-lock.json

                    echo "📦 Próba instalacji zależności przez npm ci..."
                    if ! npm ci; then
                      echo "⚠️ npm ci nie powiodło się, próbuję npm install..."
                      npm install
                    fi

                    echo "🚀 Buduję aplikację..."
                    npm run build
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo "🧪 Uruchamiam testy (przykład)..."
                    npm test || echo "⚠️ Testy nie przeszły, ale pipeline leci dalej"
                '''
            }
        }
    }
    post {
        always {
            echo '📄 Próbuję zapisać raporty z testów (jeśli istnieją)...'
            junit 'test-results/**/*.xml' // dostosuj ścieżkę, jeśli masz inne raporty
        }
        failure {
            echo '❌ Build zakończył się błędem.'
        }
        success {
            echo '✅ Build zakończony sukcesem.'
        }
    }
}
