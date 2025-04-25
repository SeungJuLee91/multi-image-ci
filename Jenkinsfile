pipeline {
    agent any

    environment {
        IMAGES = "nginx node python alpine"
    }

    stages {
        stage('Debug PCC Credentials') {
            steps {
                withCredentials([
                    string(credentialsId: 'PCC_USER', variable: 'PCC_USER'),
                    string(credentialsId: 'PCC_PASS', variable: 'PCC_PASS'),
                    string(credentialsId: 'PCC_CONSOLE_URL', variable: 'PCC_CONSOLE_URL')
                ]) {
                    script {
                        def user = env.PCC_USER
                        def pass = env.PCC_PASS
                        def url  = env.PCC_CONSOLE_URL

                        // ⚠️ 테스트 목적 외 금지. 민감 정보 노출됨.
                        sh """
                            echo "=== PCC_USER: ${user}"
                            echo "=== PCC_PASS: ${pass}"
                            echo "=== PCC_CONSOLE_URL: ${url}"
                        """
                    }
                }
            }
        }
    }
}
