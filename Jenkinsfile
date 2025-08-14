pipeline {
  agent any

  stages {
    stage('Prisma Cloud Scan (Function ZIP)') {
      steps {
        // ZIP 존재 여부 확인 (없으면 빌드 실패)
        sh 'test -f lambda_deployment.zip || { echo "ZIP not found: lambda_deployment.zip"; exit 2; }'

        // 스니펫 생성기에서 나온 함수 스캔 스텝 (경로/이름만 맞춰주세요)
        prismaCloudScanFunction(
          cloudFormationTemplateFile: '',
          functionName: 'lambda_deployment',
          functionPath: 'lambda_deployment.zip',
          logLevel: 'info',
          project: '',
          resultsFile: 'prisma-function-scan-results.json'
        )

        // (선택) 결과 파일 보관
        // archiveArtifacts artifacts: 'prisma-function-scan-results.json', onlyIfSuccessful: false
      }
    }
  }
}
