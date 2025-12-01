pipeline {
    agent any

        environment {
            // Define any environment variables here
            NETLIFY_AUTH_TOKEN = credentials('netlify-token')
            NETLIFY_SITE_ID = ('f6a0e355-0952-4ca0-af01-a0c378e269e9')
            REACT_APP_VERSION = '1.2.3'
        }

        stages {
        /* 
        stage('Cleanup Workspace') {
            steps {
                cleanupWs() // This will remove all files from the workspace
            }
        }
        */
            stage('Docker'){
                steps {
                    sh 'docker build -t my-playwright-netlify-app .' 
                    }       
            }

            stage('Build') {
                agent {
                    docker {
                        image 'node:18'
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

            stage('Deploy Staging') {
                    agent {
                        docker {
                            image 'node:18'
                            reuseNode true
                            args '-u root:root' // to run as root user
                        }
                    }
            
                    steps {
                        echo "Jenkins using docker "
                        sh '''
                            echo 'Small Change to trigger deployment'
                            #npm update
                            npm install --save-dev netlify-cli node-jq
                            node_modules/.bin/netlify --version
                            #netlify --version
                            #netlify deploy --prod --dir=build --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN
                            echo "Deploying to Staging : $NETLIFY_SITE_ID"
                            node_modules/.bin/netlify status
                            node_modules/.bin/netlify deploy --dir=build --json --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN > netlify-deploy.json
                            echo "Extracting deploy URL"
                            node_modules/.bin/node-jq -r '.deploy_url' < netlify-deploy.json > staging_url.txt
                            echo "Staging URL : "
                            cat staging_url.txt '''
                            script {
                                env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' < netlify-deploy.json", returnStdout: true)
                            }
                    }
            }

            stage('Stage e2e'){

                environment {
                    NETLIFY_AUTH_TOKEN = credentials('netlify-token')
                    NETLIFY_SITE_ID = ('f6a0e355-0952-4ca0-af01-a0c378e269e9')
                    //CI_ENVIRONMENT_URL = "https://lively-meringue-ae1414.netlify.app/"
                    CI_ENVIRONMENT_URL = "${env.STAGING_URL}"
                }

                agent {
                    docker {
                        //image 'mcr.microsoft.com/playwright:v1.39.0-jammy' 
                        image 'my-playwright-netlify-app'
                        reuseNode true
                        args '-u root:root' // to run as root user
                    }
                }
                steps {
                        sh '''
                            npx playwright test --reporter=html --project=chromium --config=./playwright.config.js
                    '''
                }
                post { 
                    always {
                        echo 'Generating PlayWright HTML Report For Staging'
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: true, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'PlayWright HTML Report - Stage', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
            }

            stage ('Approve'){
                    steps {
                        timeout(time: 1, unit: 'HOURS'){
                            input cancel: 'Reject Changes', message: 'Do you accept the current state', ok: 'I do accept'
                        }
                    }
                }

            stage('Deploy Production') {
                    agent {
                        docker {
                            image 'node:18'
                            reuseNode true
                            args '-u root:root' // to run as root user
                        }
                    }
            
                    steps {
                        echo "Jenkins using docker "
                        sh '''
                            echo 'Small Change to trigger deployment'
                            npm install --save-dev netlify-cli 
                            node_modules/.bin/netlify --version
                            echo "Deploying to Production : $NETLIFY_SITE_ID"
                            node_modules/.bin/netlify status
                            node_modules/.bin/netlify deploy --prod --dir=build --site=$NETLIFY_SITE_ID --auth=$NETLIFY_AUTH_TOKEN
                        '''
                    }
            }

            stage('Prod e2e'){

                environment {
                    NETLIFY_AUTH_TOKEN = credentials('netlify-token')
                    NETLIFY_SITE_ID = ('f6a0e355-0952-4ca0-af01-a0c378e269e9')
                    CI_ENVIRONMENT_URL = "https://lively-meringue-ae1414.netlify.app/"
                }

                agent {
                    docker {
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                        args '-u root:root' // to run as root user
                    }
                }
                steps {
                        sh '''
                            npx playwright test --reporter=html --project=chromium --config=./playwright.config.js
                    '''
                }
 
                post { 
                    always {
                        echo 'Generating PlayWright HTML Report For Prod'
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: true, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'PlayWright HTML Report - Prod', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
            }
    } 
/* 
    post { 
         always {
           echo 'Generating PlayWright HTML Report'
           //junit 'test-results-jest/junit.xml'
           //cleanupWs()
           publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: true, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'PlayWright HTML Report - Prod', reportTitles: '', useWrapperFileDirectly: true])
         }
    }
*/
}