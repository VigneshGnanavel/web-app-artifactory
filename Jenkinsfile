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
        stage('Install Snyk CLI') {
            steps {
                bat 'npm install -g snyk'
            }
        }

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/VigneshGnanavel/web-app-artifactory.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                bat './mvnw clean install'
            }
        }
        
        stage('Test') {
            steps {
                bat './mvnw test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Snyk Security Testing') {
            steps {
                withCredentials([string(credentialsId: 'snyk_test', variable: 'SNYK_API_TOKEN')]) {
                    bat "snyk auth ${env.SNYK_API_TOKEN}"
                    bat "snyk test -f -d --all-projects --json > snyk_artifact_report.json"
                }
            }
        }

        stage('Generate SBOM') {
            steps {
                bat 'syft packages dir:. --scope AllLayers -o json > java_syft_artfiact_sbom.json'
            }
        }

        stage('Upload to Artifactory') {
            steps {
                script {
                    bat "jf rt upload --url http://172.17.208.1:8082/artifactory/ --access-token ${env.ARTIFACTORY_ACCESS_TOKEN} target/demo-0.0.1-SNAPSHOT.jar web-app-artifactory/"
                    bat "jf rt upload --url http://172.17.208.1:8082/artifactory/ --access-token ${env.ARTIFACTORY_ACCESS_TOKEN} java_syft_artfiact_sbom.json web-app-artifactory/"
                    bat "jf rt upload --url http://172.17.208.1:8082/artifactory/ --access-token ${env.ARTIFACTORY_ACCESS_TOKEN} snyk_artifact_report.json web-app-artifactory/"
                }
            }
        }
    }
}
