pipeline {
  agent {
    label 'worker_node1'
  }

    stages {
        stage('Checkout') {
            steps {
                // Get some code from a GitHub repository
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/yannick-ngnomi/maven-project-antra.git']]])
        
            }
        }  
      
        stage('Build') {
            steps {

                // Run Maven on a Unix agent.
                sh "mvn clean package"

            }
        }
      
        stage('Build Docker Image') {
            steps {

              sh "docker build -t webapp:v${BUILD_NUMBER} ." 

            }
        }
      
        stage('Push Image to Docker Hub') {
            steps {
              withCredentials([usernamePassword(credentialsId: 'Docker_hub_cred', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        	    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
              }

              sh "docker tag webapp:v${BUILD_NUMBER} yngnomi/webapp:v${BUILD_NUMBER}"
              sh "docker push yngnomi/webapp:v${BUILD_NUMBER}"

            }
        }
      
        stage('Run the webapp container') {
            steps {

              sh "docker run --name webapp_v${BUILD_NUMBER} -p 8088:5000 -d yngnomi/webapp:v${BUILD_NUMBER}"

            }
        }
    }
}
