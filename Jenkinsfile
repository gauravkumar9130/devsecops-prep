pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archiveArtifacts 'target/*.jar' //so that they can be downloaded later
            }
        }
      stage('Unit Tests') {
        steps {
          sh "mvn test"
        }
      }
      stage('Docker Build and Push') {
        steps {
          withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
            sh 'docker built -t gauravkumar9130/numeric-app:""$GIT_TAG"" .'
            sh 'docker push gauravkumar9130/numeric-app:""$GIT_TAG""'
      }
    }
}
      }
  }
