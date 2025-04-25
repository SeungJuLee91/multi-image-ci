pipeline {
    agent any

    environment {
        IMAGES = "nginx node python alpine"
        // Jenkins 시스템 환경 변수에서 값을 불러옴 (credentials 대신 환경변수 사용)
        PCC_USER = credentials('PCC_USER')
        PCC_PASS = credentials('PCC_PASS')
        PCC_CONSOLE_URL = credentials('PCC_CONSOLE_URL')
    }

    stages {
        stage('Download TwistCLI') {
            steps {
                sh '''
                echo "📥 Downloading twistcli..."
                wget --no-check-certificate --header "Authorization: Basic $(echo -n $PCC_USER:$PCC_PASS | base64 | tr -d '\n')" "$PCC_CONSOLE_URL/api/v1/util/twistcli"
                chmod a+x ./twistcli
                '''
            }
        }

        stage('Build & Scan Images') {
            steps {
                script {
                    IMAGES.split().each { image ->
                        def imageName = "custom-${image}:${env.BUILD_ID}"
                        def context = "${env.WORKSPACE}/${image}"

                        sh """
                            echo "🔧 Building image: ${imageName}"
                            docker build -t ${imageName} ${context}

                            echo "🔍 Scanning image: ${imageName}"
                            ./twistcli images scan \
                                --docker-address unix:///var/run/docker.sock \
                                --address $PCC_CONSOLE_URL \
                                --user $PCC_USER \
                                --password $PCC_PASS \
                                --details ${imageName}
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            echo '🧹 파이프라인 종료. 정리 중...'
        }
    }
}
