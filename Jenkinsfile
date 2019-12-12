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
				sh "docker-credential-gcr configure-docker"
				sh "docker push gcr.io/automatic-asset-253215/a-app-image:build-id-${BUILD_NUMBER}"
			}
		}

	// 	stage ('Deploy to kubernetes'){
	// 		steps{
	// 			sh """
	// 			gcloud container clusters get-credentials cluster-1 --project automatic-asset-253215 --region us-central1
	// 			kubectl get ns
	// 			/usr/local/bin/helm init --upgrade
	// 			/usr/local/bin/helm repo add techocamp-repo https://techocamp-helm-charts.storage.googleapis.com
	// 			/usr/local/bin/helm repo update
	// 			/usr/local/bin/helm upgrade --install assessment-server techocamp-repo/techocamp-helm-repo --namespace development -f applications/assessment-server/values.yaml --set=image.tag=build-id-${BUILD_NUMBER}
	// 			"""
	// 		}
	// 	}
  // }
	}
}
