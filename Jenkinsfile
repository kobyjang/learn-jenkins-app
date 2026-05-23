pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
            reuseNode true
        }
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                    ls -al
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -al
                '''
            }
        }

        stage('E2E') {
            steps {
                sh '''
                    mkdir -p test-results

                    npm install serve

                    npx serve -s build -l 3000 > /dev/null 2>&1 &

                    sleep 3

                    npx playwright test --reporter=junit > test-results/junit.xml

                    ls -al test-results

                    cat test-results/junit.xml
                '''
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}