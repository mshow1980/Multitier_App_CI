pipeline{
    agent any 
    tools {
        maven 'mvn3'
        jdk    'jdk17'
        docker 'Docker'
    }
    stage('Checkout SCM') {
        steps {
            script{
                 checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'git@github.com:mshow1980/Multitier_App_CI.git']])
            }
        }
    }
}