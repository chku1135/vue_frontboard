pipeline {
    agent any
    environment {
        NAMESPACE = "vue-project"
    }
    stages {
        stage("Build Frontend") {
            steps {
                sh "npm install && npm run build"
            }
        }
        stage("Docker Push") {
            steps {
                sh "docker build -t 192.168.56.13:80/vue-project/frontend:latest ."
                sh "docker push 192.168.56.13:80/vue-project/frontend:latest"
            }
        }
        stage("Deploy") {
            steps {
                withEnv(["KUBECONFIG=/var/lib/jenkins/.kube/config"]) {
                    sh "helm upgrade --install frontend ./helm-chart -n ${NAMESPACE} --set deployment.timestamp=\$(date +%s)"
                }
            }
        }
    }
}
