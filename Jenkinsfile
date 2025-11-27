pipeline {
    agent any

    stages {
        /* 
        stage('Cleanup Workspace') {
            steps {
                cleanupWs() // This will remove all files from the workspace
            }
        }
        */
/* 
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u root:root' // to run as root user
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
*/
/* 
        stage('Run Multiple tests in Parallel' ) {
            parallel {
                stage('Unit Tests'){
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                            args '-u root:root' // to run as root user
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
        }
*/
/*                 
                stage('e2e'){
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                            args '-u root:root' // to run as root user
                            
                        }
                    }
                    steps {
                            sh '''
                                #npm ci
                                #npm test
                                #npm run build 
                                npm install -g serve
                                #node_modules/.bin/serve -s build &
                                serve -s build &
                                sleep 5
                                npx playwright test 
                            '''
                    }
                }
            }
*/

        stage('Deploy') {
                agent {
                    docker {
                        image 'node:18-alpine'
                        reuseNode true
                        args '-u root:root' // to run as root user
                    }
                }
        
                steps {
                    echo "Jenkins using docker "
                    sh '''
                        npm update
                        npm install --save-dev netlify-cli 
                        node_modules/.bin/netlify --version
                        #netlify --version
                        #netlify deploy --prod --dir=build --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN
                    '''
                }
        }
    }

    post { 
        always {
            echo 'Cleaning up...'
            //junit 'test-results-jest/junit.xml'
            //cleanupWs()
        }
    }
}