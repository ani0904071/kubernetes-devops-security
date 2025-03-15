pipeline {
  agent any

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

      // stage('Docker build and Push') {
      //       steps {
      //         withDockerRegistry([credentialsId: 'docker-hub', url: '']) {
      //           sh "printenv"
      //           sh 'docker build -t ani0904071/numeric-app:""$GIT_COMMIT"" .'
      //           sh 'docker push -t ani0904071/numeric-app:""$GIT_COMMIT""'
      //         }
              
      //       }
      //   } 
      stage('Docker Login & Pull') {
              steps {
                  withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'ani0904071', passwordVariable: 'Time2Play9!')]) {
                      sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                      sh 'docker pull nginx:latest'
                  }
              }
          }
    
      
    }
}
