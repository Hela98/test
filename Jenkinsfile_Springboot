pipeline {
    	agent any
	environment{
    		usernameDocker = "khouuloud"
		registry = "khouuloud/flaskex-images"
		registryCredentials = 'django'
		app=''
  }
    triggers {
        pollSCM '* * * * *'
    }
    stages {
    	
        stage('Build') {
            steps {
                sh './gradlew assemble'
            }
        }
        stage('Test') {
            steps {
                sh './gradlew test'
            }
        }
        stage('Build Docker image') {
            steps {
                sh './gradlew docker'
            }
        }
        stage('Push Docker image') {
      steps{
        echo "into deploy"
        withCredentials([string(credentialsId:"${registryCredentials}", variable: 'dockerHubPwd')]){
          sh "docker login -u ${usernameDocker} -p ${dockerHubPwd}"
          sh './gradlew dockerPush -PdockerHubUsername=${usernameDocker}'
        }
      }
        }
}
}
