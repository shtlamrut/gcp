pipeline {
    agent {
        kubernetes {}
		environment {
        PROJECT_ID = 'devops-practice-277006'
        CLUSTER_NAME = 'inftfy-cluster'
        LOCATION = 'us-central1'
        CREDENTIALS_ID = 'multi-k8s'
        } 
    }	
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("devops-practice-277006/sample-app")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://eu.gcr.io',  'gcr:multi-k8s') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                sh "kubectl apply -f  deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}    


   

