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
		  defaultContainer 'jnlp'
		  yaml '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    jenkins/kube-default: true
    app: jenkins
    component: agent
spec:
  volumes:
  - name: docker-socket
    emptyDir: {}
  containers:
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
  - name: docker
    image: docker:19.03.1
    command:     
    - cat
	args:
    - 99d
    volumeMounts:
    - name: docker-socket
      mountPath: /var/run
  - name: docker-daemon
    image: docker:19.03.1-dind
    securityContext:
      privileged: true
    volumeMounts:
    - name: docker-socket
      mountPath: /var/run
  nodeSelector:
    jk_role: slave
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: jk_role
            operator: In
            values:
            - slave
  tolerations:
  - key: "jk_role"
    operator: "Equal"
    value: "slave"
    effect: "NoSchedule"
'''
      }
	}  
	  
    stages{
		stage("git checkout"){
            steps{
                script{
        git(
            url: 'https://github.com/sandeshtamboli123/gcp.git',
            credentialsId: 'jenkins-github',
            branch: 'main'
            )
                }
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
         
		stage("docker image building"){
			steps{
			  container ('docker') {
				script{
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
