pipeline {
    agent any
    environment{
        IMAGE_NAME= 'jd09/react_app:1.9'
    }

    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage("Build application") {
            steps {
                script {
                    echo "building the spring application"

                }
            }
        }
        stage("Build Image") {
            steps {
                script {
                    echo "building the docker image and push to docker hub repositroy..."
                    //gv.buildImage()
                    withCredentials([usernamePassword(credentialsId: 'jd09', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
                        sh 'docker build -t ${IMAGE_NAME} .'
                        sh 'docker login -u $USERNAME -p $PASSWORD'
                        sh 'docker push ${IMAGE_NAME}'
                    }
                }
            }
        }
        stage("Deploy Application") {
            steps {
                script {
                    echo "deploying the spring application on ec2"
                    //gv.deployApp()
                    def dockerStop="docker stop ec2-react"
                    def dockerDelete="docker rm ec2-react"
                    def dockerCreate="docker run -p 3000:3000 --name ec2-react ${IMAGE_NAME}"

                    sshagent(['ec2-ubuntu-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@43.205.94.23  ${dockerStop}"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@43.205.94.23  ${dockerDelete}"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@43.205.94.23  ${dockerCreate}"

                    }
                }
            }
        }
    }
}
