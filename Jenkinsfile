pipeline {
    // Jenkins agent 설정 - 어떤 노드든 실행 가능
    agent any

    environment {
        // 스캔할 이미지 디렉토리 이름들 (공백으로 구분)
        IMAGE_LIST = "nginx node python alpine"
    }

    stages { 
        stage('Build & Scan Images') {
            steps {
                script {
                    // IMAGE_LIST를 공백 기준으로 나눠 각각 반복 처리
                    IMAGE_LIST.split().each { image ->
                        // 커스텀 태그 지정 (예: custom-nginx:23)
                        def tag = "custom-${image}:${env.BUILD_ID}"
                        // 각 이미지에 해당하는 Dockerfile 디렉토리 경로 설정
                        def context = "${env.WORKSPACE}/${image}"
                        // 도커 이미지 빌드 수행
                        sh "docker build -t ${tag} ${context}"
                        // Prisma Cloud 이미지 보안 스캔 수행

                        prismaCloudScanImage( 
                            //스캔할 대상 이미지 (예: 'myapp:latest')
                            image: tag,
                            //도커 데몬 주소 지정
                            dockerAddress: 'unix:///var/run/docker.sock',
                            // 이미지의 생성 시간과 관계없이 항상 스캔을 수행하도록 설정
                            ignoreImageBuildTime: true,
                            // 스캔 결과를 저장할 파일 경로
                            resultsFile: 'prisma-cloud-scan-results.json',
                            // 로그 출력 수준 설정 (error, warn, info, debug, trace 중 선택 가능, 기본값은 info)
                            logLevel: 'info'
                        )
                        //저장 파일 내용 게시
                         sh "cat prisma-cloud-scan-results.json"
                    }
                } 
            }
        }
    }
}
