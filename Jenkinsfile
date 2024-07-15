pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    CI = true
    ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
    JAVA_HOME = 'C:\\Program Files\\Eclipse Adoptium\\jdk-11.0.23.9-hotspot'
    PATH = "${env.JAVA_HOME}\\bin;${env.PATH}"
  }
  stages {
    stage('Build') {
      steps {
        bat './mvnw clean install'
      }
    }
    stage('Upload to Artifactory') {
      agent {
        docker {
          image 'releases-docker.jfrog.io/jfrog/jfrog-cli-v2:2.2.0'
          args '-v /c/ProgramData/Jenkins/.jenkins/workspace/web-app-artifactory:/workspace -w /workspace'
          reuseNode true
        }
      }
      environment {
        HOME = '/workspace' // Set the home directory to the workspace
      }
      steps {
        sh '''
          jfrog rt config --url http://localhost:8081/artifactory --access-token ${ARTIFACTORY_ACCESS_TOKEN}
          jfrog rt upload --url http://localhost:8081/artifactory --access-token ${ARTIFACTORY_ACCESS_TOKEN} /workspace/target/demo-0.0.1-SNAPSHOT.jar web-app-artifactory/
        '''
      }
    }
  }
}
