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
		        script {
    			    try {
                    		sh './gradlew test'
    			    }
    			    catch(e) {
    				    ech 'Oups ! Error catched on test stage '
    			    }
		        }
          }
      }
      stage('Build Maven Image') {
        steps {
          docker.build("${registry}")
        } 
      }
      stage('Run Maven Container') {
        steps {
          //Remove maven-build-container if it exisits
          sh " docker rm -f maven-build-container"
        
          //Run maven image
          sh "docker run --rm --name maven-build-container maven-build"
        }   
      }
      stage('Deploy Spring Boot Application') {
        steps {
         //Remove maven-build-container if it exisits
          sh " docker rm -f java-deploy-container"
          sh "docker run --name java-deploy-container --volumes-from maven-build-container -d -p 8080:8080 ${registry}"
        }
      }
      /*stage('Build Docker image') {
            steps {
                sh './gradlew docker'
            }
        }*/
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
