pipeline {
    agent any

    environment {
        IMAGES = "nginx node python alpine"
    }

    stages {
        stage('Download & Scan with TwistCLI') {
            steps {
                withCredentials([
                    string(credentialsId: 'PCC_USER', variable: 'PCC_USER'),
                    string(credentialsId: 'PCC_PASS', variable: 'PCC_PASS'),
                    string(credentialsId: 'PCC_CONSOLE_URL', variable: 'PCC_CONSOLE_URL')
                ]) {
                    script {
                        sh '''
                            echo "🔐 AUTH TEST - $PCC_USER @ $PCC_CONSOLE_URL"
                            BASIC_AUTH=$(echo -n "$PCC_USER:$PCC_PASS" | base64 | tr -d '\n')
                            wget --no-check-certificate --header "Authorization: Basic $BASIC_AUTH" "$PCC_CONSOLE_URL/api/v1/util/twistcli"
                            chmod a+x ./twistcli

                            for IMAGE in $IMAGES; do
                                TAG="custom-${IMAGE}:${BUILD_ID}"
                                docker build -t "$TAG" "$WORKSPACE/$IMAGE"

                                echo "🔎 Scanning image: $TAG"
                                ./twistcli images scan \
                                    --docker-address unix:///var/run/docker.sock \
                                    --address "$PCC_CONSOLE_URL" \
                                    --user "$PCC_USER" \
                                    --password "$PCC_PASS" \
                                    --details \
                                    "$TAG"
                            done
                        '''
                    }
                }
            }
        }
    }
}
