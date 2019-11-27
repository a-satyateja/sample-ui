pipeline {
    agent any

	stages {
		stage('Cleanup Workspace'){
			steps{
				echo 'Cleaning up the workspace'
				step([$class: 'WsCleanup'])
			}
    	} 
			
		stage ('git clone - dev') {
			steps{
				git branch: 'gcp-jenkins-dev',
				credentialsId: 'git-id',
				url: 'https://github.com/Techolution/techocamp.git'
			}
		}
		
		stage('Executing code'){
			steps{
				echo 'Baking Docker Image and Tagging with build number'
				sh """
					cd applications/assessment-server/ 
					env
					pwd
					chmod 777 gradlew 
					./gradlew build distDocker
					docker image prune -a -f
					docker images
				"""
			}
    	} 
    
		stage ('Tag Docker Image'){
			steps{
				sh """
					pwd && cd applications/assessment-server/
					pwd && docker build -t assessment-server -f Dockerfile .
					docker tag assessment-server gcr.io/techocamp-221315/assessment-server-dev:build-id-${BUILD_NUMBER}
				"""
			}
		}

		stage ('Push Docker Image to GCR'){
			steps{
				sh "gcloud config list"
				sh "docker-credential-gcr configure-docker"
				sh "docker push gcr.io/techocamp-221315/assessment-server-dev:build-id-${BUILD_NUMBER}"
			}
		}

		stage ('Deploy to kubernetes'){
			steps{
				sh """
				gcloud container clusters get-credentials techocamp-1 --project techocamp-221315 --region us-central1
				kubectl get ns
				/usr/local/bin/helm init --upgrade 
				/usr/local/bin/helm repo add techocamp-repo https://techocamp-helm-charts.storage.googleapis.com
				/usr/local/bin/helm repo update 
				/usr/local/bin/helm upgrade --install assessment-server techocamp-repo/techocamp-helm-repo --namespace development -f applications/assessment-server/values.yaml --set=image.tag=build-id-${BUILD_NUMBER}
				"""
			}
		}  

  }
}
