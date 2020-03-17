pipeline {
	agent any
	environment {
		DOCKER_CREDS = credentials('DockerHub')
		DOCKER_IMAGE_NAME = "sam93dusa/swe645"
		img = ''
	}
    stages {
	
        stage('BuildWAR') {
            steps {
				echo 'Creating the Jar ...'
				sh 'java -version'
				sh 'jar -cvf surveyform.war -C src/SurveyForm.html -C src/SurveyForm.css'
            }
        }
		
		stage("BuildPublishImage"){
			steps {
				script {
					echo "${env.BUILD_ID}"
					img = docker.build "sam93dusa/swe645:${env.BUILD_ID}"

					withDockerRegistry(credentialsId: 'docker', url: '') {
						echo "Creating docker image and pusing to docker hub ..."

						img.push "${env.BUILD_ID}"
					}
				}
			}
		}

	    
		stage("UpdateDeployment") {
			steps{
				sh 'gcloud container clusters get-credentials cluster1 --zone us-central1-a'
				sh 'kubectl config view'
				sh "kubectl get deployments"
				sh "kubectl set image deployment/survey-form-gcp survey-form-image-gcp=sam93dusa/swe645:${env.BUILD_ID}"
			}
			
		}
	}
}
