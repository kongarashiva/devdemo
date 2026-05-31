pipeline {
agent any
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
ssh ec2-user@52.66.190.156  "
docker pull $IMAGE
docker stop react-app || true
docker rm react-app || true
docker run -d \
--name react-app \
-p 3000:3000 \
$IMAGE
"
'''
}
}
}
}
