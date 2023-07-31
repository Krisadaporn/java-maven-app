
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
        IMAGE_NAME = 'krisadaporn/demo-app:java-maven-2.0'
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

                    def dockerCmd = 'docker run -p 3080:3080 -d krisadaporn/demo-app:java-maven-2.0'
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@18.170.67.222 ${dockerCmd}"
                    }
                }
            }   
        }
    }
}
