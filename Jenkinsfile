DOCKER_REGISTRY='ec2-3-93-54-88.compute-1.amazonaws.com:5000'
REMOTE_HOST='root@ec2-54-82-180-198.compute-1.amazonaws.com'
DIST_FOLDER='/var/tmp'

node {
  try {
    stage('Checkout') {
      checkout scm
    }
    stage('Environment') {
      sh 'git --version'
      echo "Branch: ${env.BRANCH_NAME}"
      sh 'docker -v'
      sh 'printenv'
    }
    stage('Build Docker test'){
      sh 'docker build -t react-test -f Dockerfile.test --no-cache . '
    }
    stage('Docker test'){
      sh 'docker run --rm react-test'
    }
    stage('Clean Docker test'){
      sh 'docker rmi react-test'
    }
    stage('Create and Push Image'){
      if(env.BRANCH_NAME == 'master'){
        sh 'docker build -t react-app --no-cache .'
        sh 'docker tag react-app ${DOCKER_REGISTRY}/react-app'
        sh 'docker push ${DOCKER_REGISTRY}/react-app'
        sh 'docker rmi -f react-app ${DOCKER_REGISTRY}/react-app'
      }
    }
    stage('Deploy'){
      if(env.BRANCH_NAME == 'master'){
          sh "ssh ${REMOTE_HOST} 'docker pull ${DOCKER_REGISTRY}/react-app:latest'"
          sh "ssh ${REMOTE_HOST} 'docker ps -a -q -f name=react-app | xargs --no-run-if-empty docker rm -v -f'"
          sh "ssh ${REMOTE_HOST} 'docker run -d -it --restart=unless-stopped -p 3000:3000 --name react-app ${DOCKER_REGISTRY}/react-app:latest'"
      }
    }
  }
  catch (err) {
    throw err
  }
}
