pipeline {
    agent any
 environment {
        IMAGE_NAME = "mariemsahli123/monprojet-spring"
        IMAGE_TAG  = "${env.BUILD_NUMBER}"
        KUBE_NAMESPACE = "devops"
        DEPLOYMENT_NAME = "spring-app"
        SERVICE_NAME = "spring-service"
    }
    stages {
        stage('Git') {
            steps {
                git branch: 'main', url: 'https://github.com/SAHLI-Mariem/projet-5eme.git'
            }
        }

        stage('Test') {
            steps {
                echo 'test valide'
            }
        }
               stage('Check user') {
    steps {
        sh 'whoami'
        sh 'groups'
        sh 'docker ps'

    }
}
  
        
         stage('Build avec Maven') {
            steps {
                sh 'mvn clean install'
            }
        }
        
    }

}
