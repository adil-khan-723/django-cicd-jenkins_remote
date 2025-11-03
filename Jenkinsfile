pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')
    }

    environment {
        DOCKER = credentials('DockerHub')
        IMAGE = 'notes-app'
        DIR = '/home/ubuntu/django-cicd-jenkins_remote'
    }

    stages {
        
        stage("code pull") {
            steps {
                echo "pulling code"
                git branch: 'main', url: 'https://github.com/adil-khan-723/django-cicd-jenkins_remote.git'
                echo "code pull successful"
            }
        }

        stage("build") {
            steps {
                echo "This is the build stage"
                sh 'docker build -t ${IMAGE} .'
            }
        }

        stage("push to dockerhub") {
            steps {
                echo "pushing the image to dockerhub"
                sh "docker login -u ${DOCKER_USR} -p ${DOCKER_PSW}"
                echo "login successful tagging the image ...."
                sh "docker image tag ${IMAGE}:latest ${DOCKER_USR}/${IMAGE}"
                echo "image tagged as: ${DOCKER_USR}/${IMAGE}"
                sh "docker push ${DOCKER_USR}/${IMAGE}"
            }
        }

        stage("deploy to ec2") {
            steps {
                sh """ 
                    if [ -d $DIR ]; then
                        echo 'source code exists pulling changs from remote' && \
                        cd $DIR && \
                        git pull
                    else 
                        echo 'directory doesn't exists cloning the repo' && \
                        cd && \
                        git clone https://github.com/adil-khan-723/django-cicd-jenkins_remote.git
                    fi """

                    sh "docker system prune -af"
                    sh "docker compose down"
                    sh "docker compose up --build -d "
            }
        }
    }
    post {
        success {
            echo "pipeline ran successfully"
        }
        failure {
            echo "pipeline failed check logs for errors"
        }
    }
}