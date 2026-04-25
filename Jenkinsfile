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
                withCredentials([usernamePassword(credentialsId: 'harbor-auth', passwordVariable: 'HARBOR_PW', usernameVariable: 'HARBOR_USER')]) {
                    sh 'echo $HARBOR_PW | docker login 192.168.56.13:80 -u $HARBOR_USER --password-stdin'
                    sh "docker build -t 192.168.56.13:80/vue-project/frontend:latest ."
                    sh "docker push 192.168.56.13:80/vue-project/frontend:latest"
                }
            }
        }
        stage("Deploy") {
            steps {
                withCredentials([file(credentialsId: 'k8s-config', variable: 'KUBECONFIG')]) {
                //withEnv(["KUBECONFIG=/var/lib/jenkins/.kube/config"]) {
                    sh "helm upgrade --install frontend ./helm-chart -n \${NAMESPACE} --set imagePullSecrets[0].name=harbor-registry-secret --set deployment.timestamp=\$(date +%s)"
                }
            }
        }
    }
}
