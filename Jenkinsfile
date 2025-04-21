pipeline {
    agent any

    environment {
        IMAGE_LIST = "nginx node python alpine"
    }

    stages {
        stage('Docker Connection Test') {
            steps {
                sh '''
                    echo '=== Current User Info ==='
                    id
                    echo '=== Docker Info ==='
                    docker info
                '''
            }
        }

        stage('Prisma Cloud Scan Images') {
            steps {
                script {
                    IMAGE_LIST.split().each { image ->
                        def tag = "custom-${image}:${env.BUILD_ID}"
                        def buildContext = "${env.WORKSPACE}/${image}"

                        // Docker 빌드
                        sh "docker build -t ${tag} ${buildContext}"

                        // Prisma Cloud 스캔
                        prismaCloudScanImage(
                            image: tag
                        )
                    }
                }
            }
        }
    }

    post {
        always {
            echo '파이프라인 종료. 정리 중...'
        }
    }
}
