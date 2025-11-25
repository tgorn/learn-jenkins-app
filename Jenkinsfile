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
                    pwd
                    ls -altr
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -al 
                '''
            }
        }
    }
}
