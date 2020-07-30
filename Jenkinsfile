pipeline {
	agent any
	  tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "Maven"
   }
	
stages {
stage('checkout') {
	steps {
	checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'git', url: 'https://github.com/sunilvirat/new.git']]])
	}
		}
stage('Build') {
    steps{
	sh label: '', script: 'mvn clean package'
			}
			
}
 stage('Build and Push Docker Image') {
      steps {
        sh label: '', script: '''docker build -t gameoflife-image:$BUILD_NUMBER .
                                 docker tag gameoflife-image:$BUILD_NUMBER docker.io/venkatasunil/gameoflife-image:$BUILD_NUMBER
                                 docker push docker.io/venkatasunil/gameoflife-image:$BUILD_NUMBER'''
      }
 }
  stage('Update Image Version') {

      steps {
          
        sh label: '', script: '''sed -i s/latest/$BUILD_NUMBER/ dockerdeploy-script'''
      }
 }
 stage('Apply Kubernetes files') {
            steps {
             sh label: '', script: '''scp dockerdeploy-script dockerswarm@172.31.33.211:/home/dockerswarm/'''
                                  
        }
     }
stage('apply docker file'){
agent {
                        label "docker"
                    }
steps{
  sh label: '', script: 'sh /home/dockerswarm/dockerdeploy-script'
  }
  }
 }
  post {
        always {
            sh label: '', script: '''sudo docker rmi gameoflife-image:$BUILD_NUMBER
sudo docker rmi venkatasunil/gameoflife-image:$BUILD_NUMBER
'''
                    }
 }
    
}
