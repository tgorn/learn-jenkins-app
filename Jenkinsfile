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
        stage('e2e Testing'){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.56.1-noble'
                    reuseNode true
                    args '-u root'
                }
            }
           steps {
                sh '''
                    npm install -g serve
                    serve -s build 
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
