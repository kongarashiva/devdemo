pipeline {
agent any
parameters {
string(
name: 'ROLLBACK_VERSION',
defaultValue: '',
description: 'Enter Docker image version to rollback'
)
}
environment {
IMAGE = "kongarashiva/react-app:${BUILD_NUMBER}"
}
stages {
stage('Build Docker Image') {
steps {
sh 'docker build -t react-app .'
}
}
stage('Docker Login') {
steps {
withCredentials([
usernamePassword(
credentialsId: 'dockerhub',
usernameVariable: 'DOCKER_USER',
passwordVariable: 'DOCKER_PASS'
)
]) {
sh '''
echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
'''
}
}
}
stage('Push Image') {
steps {
sh '''
docker tag react-app $IMAGE
docker push $IMAGE
'''
}
}
stage('Deploy To Dev') {
steps {
sh '''
ssh -i /var/lib/jenkins/.ssh/id_rsa -o StrictHostKeyChecking=no ec2-user@52.66.190.156 "
docker pull kongarashiva/react-app:${BUILD_NUMBER}
docker stop react-app || true
docker rm react-app || true
docker run -d --name react-app -p 3000:3000 kongarashiva/react-app:${BUILD_NUMBER}
"
'''
}
}
stage('Rollback') {
when {
expression {
params.ROLLBACK_VERSION?.trim()
}
}
steps {
sh """
ssh -i /var/lib/jenkins/.ssh/id_rsa -o StrictHostKeyChecking=no ec2-user@52.66.190.156 '
docker stop react-app || true
docker rm react-app || true
docker pull kongarashiva/react-app:${ROLLBACK_VERSION}
docker run -d --name react-app -p 3000:3000 kongarashiva/react-app:${ROLLBACK_VERSION}
'
"""
}
}
}
}


