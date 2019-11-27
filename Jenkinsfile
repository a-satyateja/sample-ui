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
				"""
			}
		}
		// docker tag a-app-image gcr.io/techocamp-221315/assessment-server-dev:build-id-${BUILD_NUMBER}

		// stage ('Push Docker Image to GCR'){
		// 	steps{
		// 		sh "gcloud config list"
		// 		sh "docker-credential-gcr configure-docker"
		// 		sh "docker push gcr.io/techocamp-221315/assessment-server-dev:build-id-${BUILD_NUMBER}"
		// 	}
		// }

  }
}
