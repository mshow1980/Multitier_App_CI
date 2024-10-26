pipeline {

    agent any

    tools {
        jdk 'jdk17'
        maven 'mvn3'
    }
    environment {
        SCANNER_HOME = tool 'SonarQube_Scanner'
    }
    stages {
        stage('Checkout SCM'){
            steps{
                script{
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'git@github.com:mshow1980/Multitier_App_CI.git']])
                }
            }
        }
        stage('Compiling'){
            steps{
                script{
                    sh "mvn compile"
                }
            }
        }
        stage('Maven testing'){
            steps{
                script{
                    sh "mvn -Dskiptests=true"
                }
            }
        }
        stage('OWAPS Dependency Test'){
            steps{
                script{
                dependencyCheck additionalArguments: '''
                -o "./"
                -s "./"
                -f "ALL
                --prettyPrint"''', odcInstallation: 'OWASP_DC'
                dependencyCheckPublisher pattern: 'report.xml'
                }
            }
        }
        stage('Trivy FIle Scan'){
            steps{
                script{
                    sh " trivy fs --format table -o mikey.html ."
                }
            }
        }
        stage('SOnarQube Analysis'){
            steps{
                script{
                    withSonarQubeEnv('SOnar-Token') {
                        sh ''' 
                        $SCANNER_HOME/bin/SonarQube_Scanner
                        -Dsonar.projectName=Bankapp \
                        -Dsonar.projectKey=Bankapp\
                        -Dsonar.java.binaries=target
                        '''
                    }
                }
            }
        }
    }
}