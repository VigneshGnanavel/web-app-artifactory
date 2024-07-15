pipeline {
  agent any
  
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  
  environment {
    CI = true
    ARTIFACTORY_ACCESS_TOKEN = credentials('artifactory-access-token')
    JAVA_HOME = 'C:\\Program Files\\Eclipse Adoptium\\jdk-11.0.23.9-hotspot'
    PATH = "${JAVA_HOME}\\bin;${PATH}"
  }
  
  stages {
    stage('Build') {
      steps {
        bat './mvnw clean install'
      }
    } 
      steps {
        script {
          bat "jfrog rt upload --url http://172.17.208.1:8082/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/demo-0.0.1-SNAPSHOT.jar web-app-artifactory/"
        }
      }
    }
  }
}
