pipeline {
    agent any

    environment {
        IMAGE_LIST = "nginx node python alpine"  // 빌드하고 스캔할 이미지 디렉토리 목록
    }

    stages {
        stage('Prisma Cloud Scan Images') {
            steps {
                script {
                    IMAGE_LIST.split().each { image ->
                        def tag = "custom-${image}:${env.BUILD_ID}"         // 태그에는 Jenkins Build ID 사용
                        def buildContext = "${env.WORKSPACE}/${image}"      // Dockerfile이 위치한 디렉토리

                        prismaCloudScanImage(
                            image: tag,
                            dockerfilePath: buildContext
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
