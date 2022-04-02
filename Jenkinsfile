pipeline {

  environment {
    REGISTRY_NAME = "st251/web_server"
    registryCredential = 'dockerhub-st251-jenkins'
    dockerImage = ''
  }
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  
   stages {
     stage('DockerLint') {
       steps {       
            echo 'OK' 
             }
        }
   stage('Building our image') {
            steps {
                sh "
                docker build -t ${IMAGE_NAME}${VERSION_PREFIX}${BUILD_NUMBER} ${WORKSPACE} -f Dockerfile
                "
            }
        }
   stage('push to DHub') {
            steps {
                script {
                    // Assume the Docker Hub registry by passing an empty string as the first parameter
                    docker.withRegistry('' , 'dockerhub-st251-jenkins') {
                       
                        docker push ${REGISTRY_NAME}$GIT_BRANCH-$GIT_COMMIT
                    }
                }
            }
        } 
   stage('docker run') {
            steps {
                  sh 'docker run --name web_server -p 8282:8282'
            }
        }
 
   stage('HealthCheck') {
      steps {
             
             sleep 300
             sh 'curl http://localhost:8282'
      }
   }
   stage('Remove Unused docker image') {
      steps{
        input 'Remove Unused docker image?'
        sh "docker rmi ${REGISTRY_NAME}"
      }
    }
  }
   
}
