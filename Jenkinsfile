pipeline {
  agent any

  stages {
      stage('Build Artifact') {
            steps {
              sh "mvn clean package -DskipTests=true"
              archive 'target/*.jar' //so that they can be downloaded later
            }
        }
      stage('Unit Test- Jacoco, Junit') {
            steps {
              sh "mvn test"
            }
        post {
        always {
            junit 'target/surefire-reports/*.xml'
            jacoco execPattern: 'target/jacoco.exec'
        }
        }
        }
        stage('Docker build and push') {
            steps {
              withDockerRegistry([credentialsId:"dockerhub", url:""]){
                sh 'printenv'
                sh 'docker build -t shubhendu0502/numeric-app:""$GIT_COMMIT"" . '
                sh 'docker push shubhendu0502/numeric-app:""$GIT_COMMIT""'
              }
            }
        }
        stage('Kubernetes Deployment -DEV') {
            steps {
              withKubeConfig([credentialsId:'kubeconfig']){
                sh "sed -i 's#replace#shubhendu0502/numeric-app:${GIT_COMMIT}#g' k8s_deployment_service.yaml"
                sh "kubectl apply -f k8s_deployment_service.yaml"
              }
            }
        }
    }
}
