pipeline {
    environment {
        LOCATION = "us-central1"
        PROJECT_ID = "devops-practice-277006"
		CLUSTER_NAME = 'inftfy-cluster'
        CREDENTIALS_ID = 'multi-k8s'
    }
    agent {
	  kubernetes {
		  label 'slave'
      }
	}  
	  
    stages{
		stage('Repo Clone'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                extensions: [], userRemoteConfigs: [[url:'https://github.com/sandeshtamboli123/gcp.git']]])
            }
        }
        
        stage("Building Application Docker Image"){
            steps{
			  container ('gcloud') { 
                script{
                    sh 'gcloud auth configure-docker'
                    sh 'docker build -t sample-app .'
					sh 'docker tag sample-app gcr.io/${env.PROJECT_ID}/demo-app/sample-app'
                }
              }
            }
		}

        stage("Pushing Application Docker Image to Google Artifact Registry"){
            steps{
                script{
                    sh 'docker push gcr.io/${env.PROJECT_ID}/demo-app/sample-app'
                }
			}	
        }

        stage("Application Deployment on Google Kubernetes Engine"){
            steps{
                script{
                    sh "gcloud container clusters get-credentials app-cluster --zone ${env.ZONE} --project ${env.PROJECT_ID}"
                    sh 'kubectl apply -f manifests/.'
					step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
                }
            }
        }
    }
}
