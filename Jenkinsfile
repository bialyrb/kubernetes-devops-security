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
//      stage('Docker build/push') {
//        steps {
//          script {
//            def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true)
//          }
//          sh 'docker buildx build --tag bialyrb/numeric-app:""$commitHash"" .'
//        }
//      }
      stage('Docker push') {
        steps {
          withCredentials([usernamePassword( credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
            script {
              def commitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true)
            }
            sh 'export commitHash=$(git rev-parse --short HEAD)'
            sh 'printenv'
            sh "docker login -u ${USERNAME} -p ${PASSWORD}"
            sh 'docker buildx build --file Dockerfile --pull --tag bialyrb/numeric-app:""$GIT_COMMIT"" --push .'
          }
        }
      }
    }
}
