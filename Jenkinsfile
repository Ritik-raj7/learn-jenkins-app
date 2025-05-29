pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'a1535bc1-ab2f-40ac-817b-e09a85357631'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages {
        stage('Build') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('test'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh'''
                    echo 'Test Stage'
                    test -f build/index.html
                    npm test
                '''
                
            }
            post{
                always{
                    junit 'test-results/junit.xml'
                }
            }
        }
        stage('deploy') {
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli@20.1.1
                    node_modules/.bin/netlify --version
                    echo 'Deplaoying to production . Site Id: $NETLIFY_SITE_ID'
                    npx netlify status
                '''
            }
        }
    }
    
}
