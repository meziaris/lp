pipeline {

    options{
        ansiColor('xterm')
    }
    
    agent {
        kubernetes{
            yamlFile 'builder.yaml'
        }
    }

    environment {
        KUBECONFIG = credentials('kubeconfig')
    }
        
    stages {
        // stage('Build Docker Image') {
        //     steps {
        //         sh 'docker build -t sddswd/lp:$BUILD_NUMBER .'
        //     }
        // }
        // stage('Push Image to Docker hub') {
        //     steps {
		// 		sh 'docker push sddswd/lp:$BUILD_NUMBER'
        //     }
        // }
        stage('Get All Pods') {
            steps {
                container('kubectl'){
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]){
                        sh 'kubectl get pods -A'
                    }
                }
            }
        }
        // stage('Deploy to K8S') {
        //     steps {
        //         checkout scm
        //         sh """
        //         sed -i 's/latest/$BUILD_NUMBER/g' deployment.yml
		// 		kubectl --kubeconfig $KUBECONFIG apply -f deployment.yml
        //         """
        //     }
        // }
        // stage('Remove docker image last build Dev') {
        //     steps {
        //         sh 'docker rmi sddswd/lp:$BUILD_NUMBER'
        //     }
        // }
        stage('Git') {
            steps {
                step([$class: 'WsCleanup'])
                checkout scm
            }
        }
    }
}