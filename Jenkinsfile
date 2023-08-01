
library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/Krisadaporn/jenkins-shared-library.git',
    credentialsID: 'github-repo'
    ]
)

def gv

pipeline {
    agent any
    tools {
        maven 'Maven'
    }

    environment {
        IMAGE_NAME = 'krisadaporn/demo-app:java-maven-1.0'
    }

    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage("build jar") {
            steps {
                script {
                    buildJar()
                }
            }
        }
        stage("build image") {
            steps {
                script {
                    buildImage(env.IMAGE_NAME)
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo 'deploying docker image to EC2...'
                    def dockerComposeCmd = "docker compose -f docker-compose.yaml up --detach"
                    sshagent(['ec2-server-key']) {
                        sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ec2-user@18.130.79.131:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@18.130.79.131 ${dockerComposeCmd}"
                    }
                }
            }
        }
    }
}