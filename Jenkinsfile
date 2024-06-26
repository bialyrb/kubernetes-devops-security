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
      stage('Unit tests') {
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
      stage('Docker build/push') {
        steps {
          withDockerRegistry([credentialsId: 'dockerhub', url: ""]) {
            sh 'docker buildx build --file Dockerfile --pull --tag bialyrb/numeric-app:""$GIT_COMMIT"" --push .'
          }
        }
      }
      stage('Kubernetes deployment') {
        steps {
          withKubeConfig([credentialsId: 'kubeconfig']) {
            sh "sed -i 's|replace|bialyrb/numeric-app:$GIT_COMMIT|g' k8s_deployment_service.yaml"
            sh "kubectl apply -f k8s_deployment_service.yaml"
          }
        }
      }
    }
}
