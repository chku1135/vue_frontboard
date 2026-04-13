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
                    // --set deployment.timestamp=$(date +%s)로 강제 재배포 유도
                    sh "helm upgrade --install frontend ./helm-chart -n ${NAMESPACE} --set deployment.timestamp=$(date +%s)"
                }
            }
        }
    }
}