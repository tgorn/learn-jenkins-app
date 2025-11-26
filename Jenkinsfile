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
                echo "Jenkins using docker "
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
        stage('Run Multiple tests in Parallel' ){
            parallel {
                stage('Unit Tests'){
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
                            args '-u root:root'
                        }
                    }
                    steps {
                            sh '''
                                npm ci
                                npm test 
                                npm install -g serve
                                serve -s build &
                                #node_modules/.bin/serve -s build &
                                sleep 5
                                npx playwright test
                            '''
                    }
                }
            }
        }
    }

    post { 
        always {
            echo 'Cleaning up...'
            junit 'test-results-jest/junit.xml'
            //cleanupWs()
        }
    }
}
