// pipeline {
//     agent any

//     environment {
//         NETLIFY_SITE_ID = 'a1535bc1-ab2f-40ac-817b-e09a85357631'
//         NETLIFY_AUTH_TOKEN = credentials('netlify-token')
//     }

//     stages {
//         stage('Build') {
//             agent{
//                 docker{
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     ls -la
//                     node --version
//                     npm --version
//                     npm ci
//                     npm run build
//                     ls -la
//                 '''
//             }
//         }
//         stage('test'){
//             agent{
//                 docker{
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps{
//                 sh'''
//                     echo 'Test Stage'
//                     test -f build/index.html
//                     npm test
//                 '''
                
//             }
//             post{
//                 always{
//                     junit 'test-results/junit.xml'
//                 }
//             }
//         }
//         stage('deploy') {
//             agent{
//                 docker{
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     echo 'testing'
//                     npm install netlify-cli@20.1.1
//                     node_modules/.bin/netlify --version
//                     echo 'Deplaoying to production . Site Id: $NETLIFY_SITE_ID'
//                     npx netlify status
//                     npx netlify deploy --dir=build --prod
//                 '''
//             }
//         }
//     }
    
// }
// pipeline {
//     agent any

//     environment {
//         ARTIFACTORY_URL = 'http://20.211.80.129:8082/artifactory'
//         ARTIFACTORY_REPO = 'example-repo-local'
//         ARTIFACTORY_TARGET = 'react-app/'
//         ARTIFACTORY_USER = 'admin1'
//         ARTIFACTORY_PASSWORD = 'Jfrog@123'
//     }

//     stages {
//         stage('Build') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     echo "Installing dependencies..."
//                     npm ci

//                     echo "Building React app..."
//                     npm run build

//                     echo "Listing build output..."
//                     ls -la build
//                 '''
//             }
//         }

//         stage('Test') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     echo "Running tests..."
//                     test -f build/index.html
//                     npm test
//                 '''
//             }
//             post {
//                 always {
//                     junit 'test-results/junit.xml'
//                 }
//             }
//         }

//         stage('Upload to Artifactory') {
//             agent {
//                 docker {
//                     image 'node:18-alpine'
//                     reuseNode true
//                 }
//             }
//             steps {
//                 sh '''
//                     echo "Installing JFrog CLI..."
//                     npm install -g jfrog-cli

//                     echo "Configuring JFrog CLI..."
//                     jfrog rt c my-server \
//                         --url=$ARTIFACTORY_URL \
//                         --user=$ARTIFACTORY_USER \
//                         --password=$ARTIFACTORY_PASSWORD \
//                         --interactive=false

//                     echo "Uploading build folder to Artifactory..."
//                     jfrog rt u "build/**" "$ARTIFACTORY_REPO/$ARTIFACTORY_TARGET" --server-id=my-server
//                 '''
//             }
//         }
//     }
// }
pipeline {
    agent any

    environment {
        ARTIFACTORY_URL = 'http://20.211.80.129:8082/artifactory'
        ARTIFACTORY_REPO = 'example-repo-local'
        ARTIFACTORY_TARGET = 'react-app/'
        ARTIFACTORY_USER = 'admin1'
        ARTIFACTORY_PASSWORD = 'Jfrog@123'
    }

    stages {
        stage('Build') {
            steps {
                sh '''
                    echo "Installing dependencies..."
                    npm ci

                    echo "Building React app..."
                    npm run build

                    echo "Build output:"
                    ls -la build
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    echo "Running tests..."
                    test -f build/index.html
                    npm test
                '''
            }
            post {
                always {
                    junit 'test-results/junit.xml'
                }
            }
        }

        stage('Upload to Artifactory') {
            steps {
                sh '''
                    echo "Downloading JFrog CLI binary manually..."
                    curl -fL https://releases.jfrog.io/artifactory/jfrog-cli/v2/2.58.1/jfrog-cli-linux-amd64/jfrog \
                      -o jfrog

                    chmod +x jfrog
                    ./jfrog --version

                    echo "Configuring JFrog CLI..."
                    ./jfrog rt c my-server \
                        --url=$ARTIFACTORY_URL \
                        --user=$ARTIFACTORY_USER \
                        --password=$ARTIFACTORY_PASSWORD \
                        --interactive=false

                    echo "Uploading build/ to Artifactory..."
                    ./jfrog rt u "build/**" "$ARTIFACTORY_REPO/$ARTIFACTORY_TARGET" --server-id=my-server

                    echo "Cleaning up JFrog binary..."
                    rm -f jfrog
                '''
            }
        }
    }
}

