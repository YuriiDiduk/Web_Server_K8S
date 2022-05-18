pipeline {
  agent any
    parameters {
        booleanParam (
            defaultValue: true,
            description: 'Compile and testing SonarQube',
            name: 'TEST')
        booleanParam (
            defaultValue: true,
            description: 'Build and push images to DockerHub',
            name: 'BUILD')
        booleanParam (
            defaultValue: true,
            description: 'Deploy to K8S',
            name: 'DEPLOY')
    }
     stages{
       stage('D-Build'){
            when {
                expression { params.BUILD == true }
               }
            steps {
                echo "Now we begin BUILD"
                sh "docker build -t st251/web_server:${env.BUILD_NUMBER} ."
                 }
              }
       stage('D-push') {
            when {
                expression { params.BUILD == true }
               }
           steps {
                echo "Now we begin push"
                withDockerRegistry(credentialsId: 'dockerhub-st251-jenkins', url: 'https://index.docker.io/v1/'){
                    sh "docker push st251/web_server:${env.BUILD_NUMBER}"
                    sleep 60
                    sh "docker rmi st251/web_server:${env.BUILD_NUMBER}"           
                 } 
             }
           }
        stage('Deploy'){
            when {
                expression { params.DEPLOY == true }
            }
                 withKubeConfig([credentialsId: 'config', serverUrl: 'https://0ADC4FD916ACB6CB1B1D03BFDF743B73.gr7.us-east-1.eks.amazonaws.com']) {
                   sh 'kubectl apply -f ./web_deploy.yml'
                        }
            }
        }
    }
