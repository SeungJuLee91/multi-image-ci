pipeline {
    agent any

    environment {
        IMAGE_LIST = "nginx node python alpine"
    }

    stages {
        stage('Build & Scan Images') {
            steps {
                script {
                    IMAGE_LIST.split().each { image ->
                        def tag = "custom-${image}:${env.BUILD_ID}"
                        def context = "${env.WORKSPACE}/${image}"

                        sh """
                            echo "🛠️ Building image: ${tag}"
                            docker build -t ${tag} ${context}
                        """

                        prismaCloudScanImage(
                            image: tag,
                            dockerAddress: 'unix:///var/run/docker.sock',
                            ignoreImageBuildTime: true
                        )
                    }
                }
            }
        }
    }

    post {
        always {
            echo '🧹 파이프라인 종료. 정리 중...'
        }
        success {
            echo '✅ Build was successful!'
        }
        failure {
            echo '❌ Build failed.'
        }
    }
}
