def namespace = 'wp'
def releaseName = 'final-project-wp-scalefocus'
def chartPath = '/home/ivan/Final-Project-Assessment-for-Scalefocus-Academy/charts/bitnami/wordpress'
def podName = 'wordpress'
def rolloutName = 'deployment/final-project-wp-scalefocus-wordpress'
def klusterIP = 'https://127.0.0.1:6443'

pipeline {
    agent any
    stages {
        stage('Check for existing namespace') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'Kyube', serverUrl: "${klusterIP}", restrictKubeConfigAccess : true ]) {
                        def isCreated = sh(script: "kubectl get namespaces | grep -q '${namespace}'&& echo true || echo false", returnStdout: true).trim() == 'true'
                        echo "${isCreated}"
                        if (!isCreated) {
                            sh "kubectl create namespace '${namespace}'"
                        } else {
                            echo "Namespace already exists"
                            
                        }
                    }
                }
            }
        }
        
        stage('Check for existing deployment') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'Kyube', serverUrl: "${klusterIP}", restrictKubeConfigAccess : true ]) {
                        def isCreated = sh(
                            script: "kubectl get pods -n wp | grep -q '${podName}' && echo true || echo false",
                            returnStdout: true).trim() == 'true'
                            
                        echo "${isCreated}"
                        if (isCreated == false) {
                           // sh "helm dependency update ${charPath}"
                            sh "helm install ${releaseName} ${chartPath} --namespace ${namespace}"
                        } else {
                            echo "WordPress deployment already exists"
                        }
                    }
                }
            }
        }
        
        stage('Deployment completed') {
            when {
                expression {
                    script {
                        withKubeConfig([credentialsId: 'Kyube', serverUrl: "${klusterIP}"]) {
                            def deploymentName = sh(
                                returnStdout: true,
                                script: "kubectl rollout status '${rolloutName}' -n wp | grep -q 'successfully' && echo true || echo false"
                            ) == 'true'
                            return deploymentName
                        }
                    }
                }
            }
            steps {
                echo 'Running WordPress'
            }
        }
        
        stage('Port forwarding') {
            steps {
                withKubeConfig([credentialsId: 'Kyube', serverUrl: 'https://127.0.0.1:6443']) {
                    sh 'kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 6060:80'
                }
            }
        }
    }
}