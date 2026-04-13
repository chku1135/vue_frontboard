pipeline {
    agent any
    environment {
        NAMESPACE = 'vue-project'
    }
    stages {
        stage('Build Frontend') {
            steps {
                // 프론트엔드 의존성 설치 및 빌드
                sh 'npm install && npm run build'
            }
        }
        stage('Docker Push') {
            steps {
                // 프론트엔드 이미지 빌드 및 푸시
                sh 'docker build -t 192.168.56.13:80/vue-project/frontend:latest .'
                sh 'docker push 192.168.56.13:80/vue-project/frontend:latest'
            }
        }
        stage('Deploy') {
            steps {
                // 프론트엔드 배포
                sh "helm upgrade --install frontend ./helm-chart -n ${NAMESPACE}"
            }
        }
    }
}