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
        stage('SonarQube Analysis') {
    environment {
        SONAR_HOST_URL = 'http://192.168.94.185:9000' 
        SONAR_AUTH_TOKEN = credentials('sonar-token') 
     


    }
    steps {
                sh 'mvn sonar:sonar -Dsonar.projectKey=sample_project -Dsonar.host.url=${SONAR_HOST_URL} -Dsonar.login=${SONAR_AUTH_TOKEN}'

    }
}
        
        
        
        
        
       stage('Docker') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
                sh 'docker save monprojet-spring -o /tmp/monprojet-spring.tar'

            }
        }
        stage('Push to Docker Hub') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker push ${IMAGE_NAME}:${IMAGE_TAG}
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest
                docker push ${IMAGE_NAME}:latest
                docker logout
            '''
        }
    }
}

        stage('Deploy') {
            steps {
               
                sh 'docker stop monprojet-spring || true'
                sh 'docker rm monprojet-spring || true'

                sh 'docker run -d --name monprojet-spring -p 8081:8080 ${IMAGE_NAME}:${IMAGE_TAG}'
                
            }
        }
        
        
        stage('Deploy to Kubernetes') {
            steps {
                echo '🚀 Déploiement sur Kubernetes...'
                sh '''
                    kubectl config current-context

                    kubectl set image deployment/${DEPLOYMENT_NAME} \
                        spring-app=${IMAGE_NAME}:${IMAGE_TAG} \
                        -n ${KUBE_NAMESPACE} --record

                    kubectl rollout status deployment/${DEPLOYMENT_NAME} -n ${KUBE_NAMESPACE}

                    kubectl get pods -n ${KUBE_NAMESPACE}
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline terminé avec succès. L’application est déployée sur Kubernetes (namespace: ${KUBE_NAMESPACE})."
        }
        failure {
            echo "❌ Échec du pipeline. Consulte les logs pour identifier le problème."
        }
    }

}
