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
    }
    post { 
        always {
            echo 'Cleaning up...'
            junit 'test-results.xml'
            cleanupWs()
        }
    }
}
