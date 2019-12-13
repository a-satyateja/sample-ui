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
				git branch: 'master',
				credentialsId: 'git',
				url: 'https://github.com/a-satyateja/sample-ui.git'
			}
		}

		stage('Executing code'){
			steps{
				echo 'Baking Docker Image and Tagging with build number'
				sh """
					env
					pwd
					docker image prune -a -f
					docker images
				"""
			}
    	}

		stage ('Tag Docker Image'){
			steps{
				sh """
					pwd && docker build -t a-app-image .
					docker tag a-app-image gcr.io/automatic-asset-253215/a-app-image:build-id-${BUILD_NUMBER}
				"""
			}
		}

		stage ('Push Docker Image to GCR'){
			steps{
				sh "gcloud config list"
				// sh "docker-credential-gcr configure-docker"
				sh "docker push gcr.io/automatic-asset-253215/a-app-image:build-id-${BUILD_NUMBER}"
			}
		}
// /usr/local/bin/helm init --upgrade
		stage ('Deploy to kubernetes'){
			steps{
				sh """
				gcloud container clusters get-credentials cluster-1 --project automatic-asset-253215 --region us-central1-a
				kubectl get ns

				/usr/local/bin/helm repo add ui-charts https://ui-charts.storage.googleapis.com
				/usr/local/bin/helm repo update
				/usr/local/bin/helm upgrade ui-app --install ui-charts/ui-charts -f values.yaml --set=image.tag=build-id-${BUILD_NUMBER}
				"""
			}
		}
  }
	}
}
