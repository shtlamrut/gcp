pipeline {
    agent {
        kubernetes {}
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
               extensions: [], userRemoteConfigs: [[url: 'https://github.com/sandeshtamboli123/gcp.git']]])
           }
        }
    }
}

