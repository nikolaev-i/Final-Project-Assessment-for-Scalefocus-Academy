def namespace = 'wp'
def releaseName = 'final-project-wp-scalefocus'
def chartPath = '/home/ivan/Final-Project-Assessment-for-Scalefocus-Academy/charts/bitnami/wordpress'

pipeline {
    agent any
    stages {
        stage('Apply Kubernetes files') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'Kyube', serverUrl: 'https://127.0.0.1:6443']) {
                        def isCreated = sh(script: "kubectl get namespaces | grep -q '${namespace}'&& echo true || echo false", returnStdout: true).trim() == 'true'
                        echo "${isCreated}"
                        if (!isCreated) {
                            sh "kubectl create namespace wp"
                        } else {
                            echo "Namespace already exists"
                            
                        }
                    }
                }
            }
        }
        
        stage('Check of existing WordPress deployment') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'Kyube', serverUrl: 'https://127.0.0.1:6443', restrictKubeConfigAccess : true ]) {
                        def isCreated = sh(
                            script: "helm list -q --all-namespaces | grep -q 'final-project-wp-scalefocus'&& echo true || echo false",
                            returnStdout: true).trim() == 'true'
                            
                        echo "${isCreated}"
                        if (isCreated == false) {
                            sh "helm install ${releaseName} ${chartPath} --namespace ${namespace}"
                        } else {
                            echo "WordPress deployment already exists"
                        }
                    }
                }
            }
        }
        
        stage('Run WordPress') {
            when {
                expression {
                    script {
                        withKubeConfig([credentialsId: 'Kyube', serverUrl: 'https://127.0.0.1:6443']) {
                            def deploymentName = sh(
                                returnStdout: true,
                                script: "kubectl rollout status deployment/final-project-wp-scalefocus-wordpress -n wp | grep -q 'successfully' && echo true || echo false"
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
        
        stage('Next Stage') {
            steps {
                withKubeConfig([credentialsId: 'Kyube', serverUrl: 'https://127.0.0.1:6443']) {
                    sh 'kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 6060:80'
                }
            }
        }
    }
}
