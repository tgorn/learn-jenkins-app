pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            
            steps {
                echo "Using docker "
                sh '''
                    ls -altr
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -altr 
                '''
            }
        }
        stage('Testing'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Testing index.html"
                    test  build/index.html
                    echo "Testing npm test"
                    npm ci
                    npm --version
                    node --version
                    npm test
                '''
            }
        }
        stage('e2e'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.56.1-noble'
                    reuseNode true
                }
            }
           steps {
                sh '''
                    npm install -g serve
                    node_modules/.bin/serve -s build &
                    sleep 5
                    npx playwright test
                '''
            }
        }
    }
    post { 
        always {
            echo 'Cleaning up...'
            junit 'test-results/junit.xml'
            //cleanupWs()
        }
    }
}
