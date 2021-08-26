pipeline {
    
    agent any

    environment {
        KUBECONFIG = credentials('kubeconfig')
        // registry = "sddswd/lp" 
        // registryCredential = 'dockerhublogin' 
        // dockerImage = '' 
    }
        
    stages {
        stage('Build podman Image') {
            steps {
                sh 'podman build -t sddswd/lp:$BUILD_NUMBER .'
                sh 'podman tag sddswd/lp:$BUILD_NUMBER sddswd/lp:latest'
            }
        }
        // stage('Build Image'){
        //     steps{
        //         script{
        //             dockerImage = docker.build registry + ":$BUILD_NUMBER" 
        //         }
        //     }
        // }
        stage('Push Image to podman hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhublogin', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
                    sh 'echo $USERNAME'
                    sh 'podman login docker.io -u ${USERNAME} -p ${PASSWORD}'
                    sh 'podman push sddswd/lp:$BUILD_NUMBER'
                    sh 'podman push sddswd/lp:latest'
                }
            }
        }
        // stage('Push Image to DockerHub'){
        //     steps{
        //         script{
        //             docker.withRegistry( '', registryCredential ){
        //                 dockerImage.push()
        //             }
        //         }
        //     }
        // }
        stage('Deploy to K8S') {
            steps {
                checkout scm
                sh """
                sed -i 's/latest/$BUILD_NUMBER/g' deployment.yml
				kubectl --kubeconfig $KUBECONFIG apply -f deployment.yml
                """
            }
        }
        stage('Remove podman image last build Dev') {
            steps {
                sh 'podman rmi sddswd/lp:$BUILD_NUMBER'
                sh 'podman rmi sddswd/lp:latest'
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