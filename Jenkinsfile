pipeline {
    agent any
    tools {
        nodejs 'node'
    }
    options {
        timestamps()
    }
    environment {
        CI = 'true'
        CONTAINER_NAME = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}"
        IMAGE_NAME  = "${env.BRANCH_NAME == 'main' ? 'nodemain' : 'nodedev'}:v1.0"
        HOST_PORT    = "${env.BRANCH_NAME == 'main' ? '3000' : '3001'}"
    }
    stages {
        stage('Build') {
            options {
                timeout(time: 30, unit: 'MINUTES')
            }
            steps {
                script {
                    sh '''
                      npm install
                    '''
                }
            }
        }
        stage('Test') {
            options {
                timeout(time: 30, unit: 'MINUTES')
            }
            steps {
                script {
                    sh '''
                      npm test
                    '''
                }
            }
        }
        stage('Docker build') {
            options {
                timeout(time: 30, unit: 'MINUTES')
            }
            steps {
                script {
                    sh '''
                      docker build -f Dockerfile -t ${IMAGE_NAME} .
                      docker images ${IMAGE_NAME}
                    '''
                }
            }
        }
        stage('Deploy') {
            options {
                timeout(time: 30, unit: 'MINUTES')
            }
            steps {
                    script {
                        sh '''
                          docker stop $CONTAINER_NAME || true && docker rm $CONTAINER_NAME || true
                          docker run -d --name $CONTAINER_NAME --expose $HOST_PORT -p $HOST_PORT:3000 $IMAGE_NAME
                        '''
                    }
                }
            }
        }
}

// Command for build of NodeJS application - npm install.
// Command for testing of NodeJS application - npm test.
// Build docker images:
// for main
// docker build -t nodemain:v1.0.
// for dev
// docker build -t nodedev:v1.0.
// Before you run your container you should stop and delete all previously running containers. Try to make the lowest downtime.
// Run your application in docker container:
// for main branch
// docker run -d --expose 3000 -p 3000:3000 nodemain:v1.0
// for dev branch
// docker run -d --expose 3001 -p 3001:3000 nodedev:v1.0.