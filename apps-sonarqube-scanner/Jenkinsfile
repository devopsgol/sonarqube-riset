pipeline {
    agent {
        label 'slave-devopsgol'
    }
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    stages{
        stage("checkout"){
            steps{
                checkout scm
            }
        }

        stage("Test"){
            steps{
                sh 'sudo apt install npm -y'
                sh 'npm test'
            }
        }

        stage("Build NPM"){
            steps{
                sh 'npm run build'
            }
        }

        stage('SAST SCAN BY SONARQUBE') {
            steps{
                withSonarQubeEnv('sonarqube-devops') {
                    sh "mvn package"
                    sh ''' $SCANNER_HOME/bin/sonarqube-devops -Dsonar.url=http://10.20.40.45:9000/ -Dsonarlogin=squ_84d260c50255d9dff18114aaaf0a7638cbc96fab -Dsonar.projectName=node-dockerized-projects \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=node-dockerized-projects '''
                }
            }
        }
        
        stage('Scan By snyk security vuln') {
            steps{
                snykSecurity organisation: 'devopsgol', projectName: 'synk-apps-to-html-devopsgol', severity: 'medium', snykInstallation: 'synk-devopsgol', snykTokenId: 'snyk_token', targetFile: 'package.json'
        }
        }
        stage("Build Image"){
            steps{
                sh 'sudo docker build -t my-node-app:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_cred', passwordVariable: 'DOCKERHUB_PASSWORD', usernameVariable: 'DOCKERHUB_USERNAME')]) {
                    sh 'sudo docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD'
                    sh 'sudo docker tag my-node-app:latest adinugroho251/my-node-app:latest'
                    sh 'sudo docker push adinugroho251/my-node-app:latest'
                    sh 'sudo docker logout'
                }
            }
        }

      stage('Docker RUN') {
          steps {
      	     sh 'sudo docker run -d -p 3000 --name deploy-apps-nodejs-devopsgol-test-successfully  adinugroho251/my-node-app:latest'
      }
    }
}    
}

