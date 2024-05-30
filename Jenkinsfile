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
    }
}
