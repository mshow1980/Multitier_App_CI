pipeline {

    agent any
    parameters {
        string(name: 'DOCKER_TAG', defaultValue: 'latest', description:'Docker tag')
    }

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
                    sh "mvn test -DskipTests=true"
                }
            }
        }
        stage('OWAPS Dependency Test'){
            steps{
                script{
                dependencyCheck additionalArguments: '''
                -o "./"
                -s "./"
                -f "ALL"
                --prettyPrint"''', odcInstallation: 'OWASP_DC'
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
                }
            }
        }
        stage('Trivy FIle Scan'){
            steps{
                script{
                    sh "trivy fs --format table -o mikey.html."
                }
            }
        }
        stage('SOnarQube Analysis'){
            steps{
                script{
                    withSonarQubeEnv('SOnar-Token') {
                        sh ''' 
                        $SCANNER_HOME/bin/SonarQube_Scanner
                        -Dsonar.projectName=bankapp \
                        -Dsonar.projectKey=bankapp\
                        -Dsonar.java.binaries=target
                        '''
                    }
                }
            }
        }
        stage('Building Artifect Deployment'){
            steps{
                script{
                    withMaven(globalMavenSettingsConfig: 'maven_for_scion_scope', jdk: 'jdk17', maven: 'mvn3', mavenSettingsConfig: '', traceability: true) {
                        sh 'mvn deploy -DskipTests=true'
                    }
                }
            }
        }
        stage('Building Docker Image'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'Docker-login', toolName: 'Docker') {
                       sh 'docker build -t mshow1980/bankapp:${params.DOCKER_TAG} .'
                       sh 'docker push  mshow1980/bankapp:${params.DOCKER_TAG}'
                    }
                }
            }
        }
        stage('Docker Image Scan'){
            steps{
                script{
                   sh "trivy image mshow1980/bankapp:${params.DOCKER_TAG} --format tabke -o image.html"
                }
            }
        }
        stage('docker Image Push'){
            steps{
                script{
                    sh 'docker push mshow1980/bankapp:${params.DOCKER_TAG}'
                }
            }
        }
    }
}