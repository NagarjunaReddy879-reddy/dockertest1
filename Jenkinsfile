pipeline {
 
   environment {   
      registry = "nagarjuna/Docker-prod"
      registryCredential = "dockerhub.id"
      devcontext = "dev-context"
      prodcontext = "prod-context"
      devnode = "172.25.0.11"
      prodnode = "172.25.0.12"
      dockerImage = ""
   }

   agent any
   stages {
      stage('Building Docker-Dev Branch') {
          when { 
               expression {
                 return.env.BRANCH_NAME != 'Docker-Prod'
          }
          steps {
             script {
                 dockerImage = docker.build registry = ":v$BUILD_NUBER"
             }
          }    
      }
      stage(''Push the Image to the DockerHub)
           steps {
              script {
                  docker.withRegistry( '', registeryCredential ) {
                    dockerImage.push()
              }
           }
      }
      stage('Cleaning up') {
           steps {
              sh "docker rmi $registry:v$BUILD_NUMBER"
           }
      }
      stage('Deploying to Prod Docker Swarm') {
           steps {
             sh "docker context user $prodcontext"
             sh "docker service rm testing1 || true"
             sh "docker service create --name testing1 -p 8000:80 $registry:v$BUILD_NUMBER"
           }
      }
      Stage('Verifying the deployment') {
          steps {
            sh 'curl http://$dockerhost:8000 || exit'
           }
      }
      Stage('Change docker context to default') {
          steps {
            sh 'docker context use default'
           }
      } 
   }           
}
