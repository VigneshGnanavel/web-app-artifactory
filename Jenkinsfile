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
        
        stage('Git Commit and Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'jenkins', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        bat 'git config --global user.name "VigneshGnanavel"'
                        bat 'git config --global user.email "prathvikvignesh@gmail.com"'
                        bat 'git checkout -B results'
                        bat 'git add -f target/demo-0.0.1-SNAPSHOT.jar'
                        bat 'git commit -m "Adding test results"'
                        bat "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/VigneshGnanavel/web-app-artifactory.gitt"
                    }
                }
            }
        }
        
        stage('Upload to Artifactory') {
            steps {
                script {
                    bat "jf rt upload --url http://172.17.208.1:8082/artifactory/ --access-token ${ARTIFACTORY_ACCESS_TOKEN} target/demo-0.0.1-SNAPSHOT.jar web-app-artifactory/"
                }
            }
        }
    }
}
