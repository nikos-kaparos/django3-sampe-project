pipeline {

    agent any

    environment {
            DOCKER_TOKEN = credentials('docker-push-secret')
            DOCKER_USER = 'nikos-kaparos'
            DOCKER_SERVER = 'ghcr.io'
            DOCKER_PREFIX = 'ghcr.io/nikos-kaparos/django'
        }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: 'git@github.com:nikos-kaparos/django3-sampe-project.git'
            }
        }
    
        


        stage('docker build') {
            steps {
                sh '''
                    HEAD_COMMIT=$(git rev-parse --short HEAD)
                    TAG=$HEAD_COMMIT-$BUILD_ID
                    docker build --rm -t $DOCKER_PREFIX:$TAG -t $DOCKER_PREFIX:latest -f nonroot-alpine.Dockerfile .
                    cat $DOCKER_TOKEN
                    cat $DOCKER_TOKEN | docker login $DOCKER_SERVER -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_PREFIX --all-tags
                '''
            }
        }

        stage('test') {
            steps {
                sh '''
                    HEAD_COMMIT=$(git rev-parse --short HEAD)
                    TAG=$HEAD_COMMIT-$BUILD_ID
                    cd myproject
                    cp myproject/.env.example myproject/.env
                    docker run --env-file ./myproject/.env $DOCKER_PREFIX:$TAG python manage.py test

                '''
            }
        }    
            
    }
}