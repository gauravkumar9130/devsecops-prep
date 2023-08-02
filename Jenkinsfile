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
        post {
          always {
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'target/jacoco.exec'
  }
    }
  }

  stage('SonarQube - SAST') {
   steps {
    sh "mvn sonar:sonar -Dsonar.projectKey=numeric-applications-Dsonar.projectName='numeric-applications' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_0e2df17bd5f4f4c764570bf3eeccfd211cb1ba28"
    }
 }

  stage('Mutation Tests - PIT') {
   steps {
     sh "mvn org.pitest:pitest-maven:mutationCoverage"
     }
     post {
      always {
        pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
        }
        }
        }
  
  stage('Docker Build and Push') {
        steps {
          withDockerRegistry([credentialsId: "dockerhub", url: ""]) {
            sh 'docker build -t gauravkumar9130/numeric-app:""$GIT_COMMIT"" .'
            sh 'docker push gauravkumar9130/numeric-app:""$GIT_COMMIT""'
      }
    }
}
    stage('Kubernetes Deployment - Dev') {
      steps {
        withKubeConfig([credentialsId: 'kubeconfig']) {
          sh "sed -i 's#replace#gauravkumar9130/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
          sh "kubectl apply -f k8s_deployment_service.yaml"
      }
  }
    }
  }
}
