pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
      jdk 'jdk17'
      maven 'maven3'
  }
  stages {
      stage("Cleanup Workspace"){
              steps {
              cleanWs()
            }
      }

    stage("Checkout from SCM"){
            steps {
              git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/bapaksukareview/registration-app.git'
            }
    }

    stage("Build Application"){
        steps {
            sh "mvn clean package"
        }
      
      }
    
    stage("Test Application"){
        steps {
             sh "mvn test"
        }
      }

    stage('SonarQube Analysis') {
        steps {
            script {
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                    sh  "mvn sonar:sonar"
                    }
                }
            }
        }
    }
}
