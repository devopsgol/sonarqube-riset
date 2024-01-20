pipeline {
    agent {
        label 'slave-devopsgol'
    }

    tools {
         jdk 'jdk17'
          maven 'maven3'
    }

    environment {
        SCANNER_HOME=tool 'sonarqube-devops'
    }

    stages {
        
        stage("Git Checkout"){
            steps{
                checkout scm
            }
        }
        
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
        stage("Test Cases"){
            steps{
                sh "mvn test"
            }
        }
        
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonarqube-devops') {
                    sh "mvn package"
                    sh ''' mvn clean verify sonar:sonar -Dsonar.projectKey=spring-framework-petclinic -Dsonar.projectName='spring-framework-petclinic' -Dsonar.host.url=http://10.20.40.45:9010 -Dsonar.token=sqp_ab06d94baeebff2101e5c7a71945e12d7a32a6e2 \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=node-dockerized-projects '''
                }
            }
        }
        
        stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }
        
        stage("Docker Build & Push"){
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker_cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    sh "docker build -t apps-for-sonarqube-riset-devopsgol ."
                    sh 'sudo docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                    sh 'sudo docker tag apps-for-sonarqube-riset-devopsgol:latest adinugroho251/apps-for-sonarqube-riset-devopsgol:latest'
                    sh "sudo docker push adinugroho251/apps-for-sonarqube-riset-devopsgol:latest"
                }
            }
        }
        
        stage('Docker RUN') {
            steps{
                sh 'sudo docker run -d -p 3000 --name apps-for-sonarqube-riset-devopsgol  adinugroho251/apps-for-sonarqube-riset-devopsgol:latest'
            }
        }        
    }
}
