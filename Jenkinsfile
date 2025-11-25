pipeline {
    agent any

    stages {
        stage('w/o docker ') {
            steps {
                echo "Without docker "
                sh '''
                pwd
                touch container-no.txt
                ls -altr
                '''
            }
        }
        
        stage('with docker ') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            
            steps {
                echo "With using docker "
                sh '''
                    ls -latr 
                    node --version 
                    npm --version
                    pwd
                    touch container-yes.txt
                    ls -altr
                '''
            }
        }
    }
}
