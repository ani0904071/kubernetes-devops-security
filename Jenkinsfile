pipeline {
  agent any
  environment {
        DOCKER_REGISTRY = "your.registry.com"  // Set your registry URL
        IMAGE_NAME = "numeric-app"
        IMAGE_TAG = "latest"
    }

  stages {
      stage('Build Artifact') {
            steps {
              // sh "mvn clean package"
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
    
      stage('Unit Test') {
            steps {
              sh "mvn test"
            }
            post {
              always {
                junit 'target/surefire-reports/*.xml'
                jacoco execPattern: 'target/jacoco.exec'
              }
              
            }
        }

      stage('Vulnerability Scans - Docker') {
            steps {
                sh "mvn dependency-check:check"               
            }
            //  post {
            //   always {
            //     dependencyCheckPublisher pattern: 'target/dependency-check-report.xml'
            //   }
            // }
          }

      stage('Docker build and Push') {
            steps {
              withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
                sh "printenv"
                sh 'docker build -t ani0904071/numeric-app:""$GIT_COMMIT"" .'
                sh 'docker push ani0904071/numeric-app:""$GIT_COMMIT""'
              }
              
            }
          } 

      stage('Kubernetes Deploymnet') {
              steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                  sh "sed -i 's#replace#ani0904071/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                  sh "kubectl apply -f k8s_deployment_service.yaml"
                }
                
              }
          } 
      // stage('Login to Docker Registry') {
      //       steps {
      //           // Authenticate to Docker registry
      //           withDockerRegistry([credentialsId: 'docker-hub', url: ""]) {
      //               echo "Logged into Docker registry successfully."
      //           }
      //       }
      //   }
        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             // Build Docker image
        //             sh 'docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:$IMAGE_TAG .'
        //         }
        //     }
        // }
        // stage('Push Docker Image') {
        //     steps {
        //         script {
        //             // Push Docker image to registry
        //             sh 'docker push $DOCKER_REGISTRY/$IMAGE_NAME:$IMAGE_TAG'
        //         }
        //     }
        // }
    
      
    }
}
