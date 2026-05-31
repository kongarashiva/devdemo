pipeline {
agent any
stages {
stage('Build Docker Image') {
steps {
sh 'docker build -t react-app .'
}
}
stage('Run Container') {
steps {
sh '''
docker stop react-app || true
docker rm react-app || true
docker run -d --name react-app -p 3000:3000 react-app
'''
}
}
}
}
