pipeline {
    agent any
    stages {
        stage('Apply Kubernetes files') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'Kyube', serverUrl: 'https://127.0.0.1:6443']) {
                        def namesp = sh(script: "kubectl get namespaces | grep 'wp'", returnStdout: true) == 0
                        if (namesp) {
                            echo "Namespace exists"
                        } else {
                            sh "kubectl create namespace wp"
                        }
                    }
                }
            }
        }
        
        stage('Check of existing wordpress') {
            steps{
                script {
                    
                }
            }
        }
    }
}
