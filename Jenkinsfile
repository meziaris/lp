pipeline {
    
    agent any

    environment {
        KUBECONFIG = credentials('kubeconfig')
    }
        
    stages {
        stage('Build docker Image') {
            steps {
                sh 'docker build -t meziaris/lp:$BUILD_NUMBER .'
                sh 'docker tag meziaris/lp:$BUILD_NUMBER meziaris/lp:latest'
            }
        }
        stage('Push Image to dockerhub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhublogin', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
                    sh 'docker login docker.io -u ${USERNAME} -p ${PASSWORD}'
                    sh 'docker push meziaris/lp:$BUILD_NUMBER'
                    sh 'docker push meziaris/lp:latest'
                }
            }
        }
        stage('Deploy to K8S') {
            steps {
                checkout scm
                sh """
                sed -i 's/latest/$BUILD_NUMBER/g' deployment.yml
				kubectl --kubeconfig $KUBECONFIG apply -f deployment.yml
                """
            }
        }
        stage('Remove docker image last build') {
            steps {
                sh 'docker rmi meziaris/lp:$BUILD_NUMBER'
                sh 'docker rmi meziaris/lp:latest'
            }
        }
        stage('Git') {
            steps {
                step([$class: 'WsCleanup'])
                checkout scm
            }
        }
    }
}
