pipeline {
    agent any
 
     environment {
        IMAGE_NAME = "taye97/pythonapp"
        TAG = "${BUILD_NUMBER}"
    }
    stages {
        stage("Build") {
            steps {
                echo "Building docker image..."
                script {
                    image = docker.build("${IMAGE_NAME}:${TAG}")
                }
            }
        }
        stage("Test") {
            steps {
                echo "Scanning docker image ..."
                withCredentials([string(credentialsId: 'snyk-token', variable: 'SNYK_TOKEN')]) {
                    sh '''
                        snyk auth $SNYK_TOKEN
                        snyk container test ${IMAGE_NAME}:${TAG} --severity-threshold=high || true
                    '''
                }
            }
        }
        stage("Login to DockerHub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
                }
            }
        }
        stage("Push") {
            steps {
                echo "Logging to DockerHub"
                script {
                    image.push()
                }
            }
        }
        stage("Deploy to VM") {
            steps {
                sshagent(['my-ssh-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no taye@192.168.42.128 '
                            docker pull taye97/pythonapp:${TAG} &&
                            docker stop pythonapp || true &&
                            docker rm pythonapp || true &&
                            docker run -d --name pythonapp -p 80:5000 taye97/pythonapp:${TAG}
                        '
                    """
                }
            }
        }
    }
}
