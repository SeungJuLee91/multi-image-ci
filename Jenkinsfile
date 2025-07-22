pipeline {
  agent any

  stages {
    stage('Prisma Cloud Scan') {
      steps {
        script {
          // 원본 이미지: 전체 태그 포함
          def fullImage = 'nvidia/cuda:12.2.0-devel-ubuntu22.04'

          // 커스텀 태그 생성
          def tag = "nvidia/cuda:${env.BUILD_ID}"

          // 이미지에 커스텀 태그 부여
          sh "docker tag ${fullImage} ${tag}"

          // Prisma Cloud 이미지 스캔
          prismaCloudScanImage(  
            image: tag,                                     //스캔할 대상 이미지 (예: 'myapp:latest')
            dockerAddress: 'unix:///var/run/docker.sock',   // Docker 데몬 소켓 주소 (로컬 Docker 엔진과 통신하기 위한 기본 경로)
            ignoreImageBuildTime: true,                     // 이미지의 생성 시간과 관계없이 항상 스캔을 수행하도록 설정
            resultsFile: 'prisma-cloud-scan-results.json',  // 스캔 결과를 저장할 파일 경로 (JSON 형식, Jenkins 작업 디렉토리에 생성됨)
            logLevel: 'info'                       // 로그 출력 수준 설정 (error, warn, info, debug, trace 중 선택 가능, 기본값은 info)
          )
          // 스캔 완료 후 이미지 삭제
          sh "docker rmi -f ${tag} || true"
        }
      }
    }
  }
}
