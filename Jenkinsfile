pipeline {

    agent any

    tools {
        jdk 'jdk17'
        maven 'mvn3'
    }
    stages {
        stage('Checkout SCM'){
            steps{
                script{
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'git@github.com:mshow1980/Multitier_App_CI.git']])
                }
            }
        }
    }
}