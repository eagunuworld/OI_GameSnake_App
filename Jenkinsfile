//@Library('shared-mss') _

pipeline{

  agent{
     label "	kubeadmAgent"
       }

    options {
      buildDiscarder logRotator(artifactDaysToKeepStr: '1', artifactNumToKeepStr: '1', daysToKeepStr: '1', numToKeepStr: '2')
      timestamps()
     }

    environment {
              DEPLOY = "${env.BRANCH_NAME == "python-dramed" || env.BRANCH_NAME == "master" ? "true" : "false"}"
              NAME = "${env.BRANCH_NAME == "python-dramed" ? "example" : "example-staging"}"
              VERSION = "${env.BUILD_ID}"
              BUILD_NUMBER = "${env.BUILD_NUMBER}"
              BRANCH_NAME = "${env.BRANCH_NAME}"
              NODE_NAME = "${env.NODE_NAME}"
              REGISTRY = 'eagunuworld/demo-gamesnake'
              REGISTRY_CREDENTIAL = 'eagunuworld_dockerhub_creds'
            }

    stages {
      stage('checking.. out codes ') {
          steps {
                git branch: 'walmart-prod', credentialsId: '	eagunuworld_dockerhub_creds', url: 'https://github.com/eagunuworld/OI_GameSnake_App.git'
                 //git (credentialsId: 'democalculus_github_creds_ID', url: 'https://github.com/democalculus/springboot-dockerimage',branch: 'walmart-dev-mss')
                   }
                }

  stage('Building Docker Images') {
          steps {
              sh "docker build -t ${REGISTRY}:${VERSION} ."
                  }
              }

    stage('Push Docker Image To DockerHub') {
          steps {
                   withCredentials([string(credentialsId: 'eagunuworld_dockerhub_creds', variable: 'eagunuworld_dockerhub_creds')])  {
                   sh "docker login -u eagunuworld -p ${eagunuworld_dockerhub_creds} "
                   }
                 sh 'docker push ${REGISTRY}:${VERSION}'
                }
             }

    stage('Display All Files In Console') {
            steps {
               sh 'ls -lart'
            }
          }

  stage('Update docker-compose Tag'){
      steps{
          	script{
          				    sh '''final_tag=$(echo $VERSION | tr -d ' ')
          				     echo ${final_tag}test
          				     sed -i "s/BUILD_TAG/$final_tag/g"  docker-compose.yml
          				     '''
          				  }
          			 }
          		}
  stage('Display docker-compose content ') {
        steps {
            sh 'cat docker-compose.yml'
            }
        }
 
 //  stage('Stop And Remove Running Container') {
 //      steps{
 //          sshagent(['ec2-user-password-credentials']) {
 //               sh 'docker ps -f name=springboot -q | xargs --no-run-if-empty docker container stop'
 //               sh 'docker container ls -a -fname=springboot  -q | xargs -r docker container rm'
 //               sh 'docker container ls '
 //                  }
 //                }
 //             }
 //
 //  stage('Remove All Images Before Deployment') {
 //        steps{
 //            sshagent(['ec2-user-password-credentials']) {
 //              sh 'docker rmi  $(docker images -q)'
 //                  }
 //               }
 //             }
 //
 // stage('Deploy On Prod') {
 //     steps{
 //       sshagent(['ec2-user-password-credentials']) {
 //            sh "scp -o StrictHostKeyChecking=no docker-compose.yml ec2-user@18.219.210.241:"
 //            sh "ssh -o StrictHostKeyChecking=no ec2-user@18.219.210.241 docker-compose up -d"
 //           }
 //         }
 //      }
 //
  stage('Remove images from Agent Server') {
        steps{
            script {
                sh 'docker rmi  $(docker images -q)'
                  }
              }
            }

 //  stage('Remove ps from Agent Server') {
 //        steps {
 //              sh 'docker ps -f name=framed -q | xargs --no-run-if-empty docker container stop'
 //              sh 'docker container ls -a -fname=framed  -q | xargs -r docker container rm'
 //              sh 'docker container ls '
 //                }
 //            }

 stage('prod-deployment') {
         steps {
            sh 'docker-compose up -d'
         }
       }

   }// stages blocks closed
}//pipeline closedd
