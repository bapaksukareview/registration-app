pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
      jdk 'jdk17'
      maven 'maven3'
    }
    environment {
        APP_NAME = "registration-app"
        RELEASE = "1.0.0"
        DOCKER_USER = "bapaksukareview"
        DOCKER_PASS = "docker-hub"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
      
    stage("Quality Gate"){
        steps {
            script {
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        
        } 
        
    stage("Build & Push Docker Image"){
        steps {
            script {
                docker.withRegistry('',DOCKER_PASS){
                    docker_image = docker.build "${IMAGE_NAME}"
                }

                docker.with Registry('',DOCKER_PASS){
                    docker_image.push("${IMAGE_TAG}")
                    docker_image.push('latest')
                }
            }
        }
    
        }
    }
}
