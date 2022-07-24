pipeline {

  environment {
    dockerimagename = "saurabhdocker13/node-app"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Source Code Fetch') {
      steps {
        git branch: 'master', url: 'https://github.com/saurabhshende/JenkinsDemo.git'
      }
    }

    stage('SonarQube') {
      environment {
        scannerHome = tool 'Sonarqube_4.7.0'
      }
      steps {
        withSonarQubeEnv('sonarscanner') {
         sh '''
         ${scannerHome}/bin/sonar-scanner \
         -D sonar.projectKey=sonar \
         -D sonar.projectVersion=1.0 \
         -D sonar.projectName=sonarscanner \
         '''
        }
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        sh 'kubectl apply -f deploymentservice.yml'
      } 
    }
  }
}
