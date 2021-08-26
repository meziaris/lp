pipeline {

  options {
    ansiColor('xterm')
  }

  agent {
    kubernetes {
      yamlFile 'builder.yaml'
    }
  }

  stages {
    stage('Kaniko Build & Push Image') {
      steps {
        container('kaniko') {
          script {
            sh '''
            /kaniko/executor --dockerfile `pwd`/Dockerfile \
                             --context `pwd` \
                             --destination=sddswd/lp:${BUILD_NUMBER}
            '''
          }
        }
      }
    }
    stage('Get All Pods') {     
      steps {
        container('kubectl') {
          withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh 'kubectl get pods -A'
          }
        }
      }
    }
    stage('Deploy App to Kubernetes') {     
      steps {
        container('kubectl') {
          withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh 'sed -i "s/latest/${BUILD_NUMBER}/" deployment.yml'
            sh 'kubectl apply -f deployment.yml'
          }
        }
      }
    }
    stage('Git'){
      steps{
        step([$class: 'WsCleanup'])
        checkout scm
      }
    }
  
  }
}