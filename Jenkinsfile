pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              script {
                  def archiveString = 'target/*.jar'
                  archiveArtifacts artifacts: archiveString, fingerprint: true
              }
            }
        }
//      stage('Unit tests') {
//            steps {
//              sh "mvn test"
//            }
//            post {
//              always {
//                junit 'target/surefire-reports/*.xml'
//                jacoco execPattern: 'target/jacoco.exec'
//              }
//            }
//        }
      stage('Docker build') {
        steps {
          sh 'printenv'
          sh 'DOCKER_BUILDKIT=1 docker buildx build --tag bialyrb/numeric-app:""$GIT_COMMIT"" .'
        }
      }
      stage('Docker push') {
        steps {
          withCredentials([usernamePassword( credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            sh 'printenv'
            sh "docker login -u ${USERNAME} -p ${PASSWORD}"
            sh 'docker push -t bialyrb/numeric-app:""$GIT_COMMIT""'
          }
        }
      }
    }
}
