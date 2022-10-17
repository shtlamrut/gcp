def PROJECT_ID = 'devops-practice-277006'
def CLUSTER_NAME = 'inftfy-cluster'
def LOCATION = 'us-central1'
def  CREDENTIALS_ID = 'multi-k8s'

pipeline {
    agent {
        kubernetes {
        defaultContainer 'jnlp'
		}
    }	
    stages {
        stage("Workspace_cleanup"){
        //Cleaning WorkSpace
            steps{
               step([$class: 'WsCleanup'])
            } 
        }
		stage('Repo Clone'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                extensions: [], userRemoteConfigs: [[url:'https://github.com/sandeshtamboli123/gcp.git']]])
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
                    docker.withRegistry('https://gcr.io',  'gcr:multi-k8s') {
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


   

