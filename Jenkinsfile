pipeline {
    agent {
        kubernetes {
            // Rather than inline YAML, in a multibranch Pipeline you could use: yamlFile 'jenkins-pod.yaml'
            // Or, to avoid YAML:
            // containerTemplate {
            //     name 'shell'
            //     image 'ubuntu'
            //     command 'sleep'
            //     args 'infinity'
            // }
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
