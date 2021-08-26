pipeline {

  options {
    ansiColor('xterm')
  }

  // agent {
  //   kubernetes {
  //     yamlFile 'builder.yaml'
  //   }
  agent {
    kubernetes {
      //cloud 'kubernetes'
      defaultContainer 'kaniko'
      yaml """
kind: Pod
spec:
  serviceAccountName: jenkins-sa
  containers:
  - name: kaniko
    image: gcr.io/kaniko-project/executor:debug-539ddefcae3fd6b411a95982a830d987f4214251
    imagePullPolicy: Always
    command:
    - sleep
    args:
    - 9999999
"""
    }

  // stages {

  //   stage('Kaniko Build & Push Image') {
  //     steps {
  //       container('kaniko') {
  //         script {
  //           sh '''
  //           /kaniko/executor --dockerfile `pwd`/Dockerfile \
  //                            --context `pwd` \
  //                            --destination=sddswd/lp:${BUILD_NUMBER}
  //           '''
  //         }
  //       }
  //     }
  //   }

    stage('Get Pods') {     
      steps {
        container('kubectl') {
          withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh 'kubectl get pods -A'
          }
        }
      }
    }
    // stage('Deploy App to Kubernetes') {     
    //   steps {
    //     container('kubectl') {
    //       withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
    //         sh 'sed -i "s/latest/${BUILD_NUMBER}/" deployment.yml'
    //         sh 'kubectl apply -f deployment.yml'
    //       }
    //     }
    //   }
    // }
  
  }
}