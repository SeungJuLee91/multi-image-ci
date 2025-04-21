pipeline {
    agent any

    environment {
        IMAGE_LIST = "nginx" // 테스트용 이미지
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

                        // 이미지 빌드 + 스캔 순차 실행
                        sh """
                            docker build -t ${tag} ${buildContext}
                            twistcli images scan \\
                                --docker-address unix:///var/run/docker.sock \\
                                --address https://a79b177c21e6f47d28b5b8ec37062fd1-1993644220.us-east-1.elb.amazonaws.com:8083 \\
                                --user ${PRISMA_USER} \\
                                --password ${PRISMA_PASS} \\
                                --details \\
                                ${tag}
                        """
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
