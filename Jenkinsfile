pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }
    triggers {
    pollSCM('H/2 * * * *')
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Đang tải source code từ GitHub'

                script {
                    def scmInfo = checkout scm

                    env.SOURCE_COMMIT = scmInfo.GIT_COMMIT
                    env.SOURCE_BRANCH = scmInfo.GIT_BRANCH

                    echo "Commit đang build: ${env.SOURCE_COMMIT}"
                    echo "Branch đang build: ${env.SOURCE_BRANCH}"
                }
            }
        }

        stage('Inspect') {
            steps {
                echo 'Đang kiểm tra source code'

                sh '''
                    echo "Workspace hiện tại:"
                    pwd

                    echo "Các file đã tải:"
                    ls -la

                    test -f Jenkinsfile
                    test -f message.txt

                    echo "Nội dung message.txt:"
                    cat message.txt
                '''
            }
        }
        
stage('Shell Basics') {
    steps {
        echo 'Đang thực hành Shell cơ bản'

        sh '''
            set -eu

            PROJECT_NAME="jenkins-shell-lab"
            REPORT_DIR="output"
            REPORT_FILE="$REPORT_DIR/shell-report.txt"

            MESSAGE=$(paste -sd " " message.txt)

            mkdir -p "$REPORT_DIR"

            echo "=== BIEN TRONG SHELL ==="
            echo "Project tự tạo: $PROJECT_NAME"
            echo "Jenkins Job: $JOB_NAME"
            echo "Jenkins Build: $BUILD_NUMBER"
            echo "Jenkins Workspace: $WORKSPACE"

            echo "=== DAU NHAY ==="
            echo "Nháy đôi sẽ thay biến: $PROJECT_NAME"
            echo 'Nháy đơn giữ nguyên: $PROJECT_NAME'

            echo "=== COMMAND SUBSTITUTION ==="
            echo "Message lấy từ file: $MESSAGE"

            echo "=== TAO BAO CAO ==="

            echo "Project=$PROJECT_NAME" > "$REPORT_FILE"
            echo "Job=$JOB_NAME" >> "$REPORT_FILE"
            echo "Build=$BUILD_NUMBER" >> "$REPORT_FILE"
            echo "Workspace=$WORKSPACE" >> "$REPORT_FILE"
            echo "Message=$MESSAGE" >> "$REPORT_FILE"

            echo "=== NOI DUNG BAO CAO ==="
            cat "$REPORT_FILE"

            test -s "$REPORT_FILE"

            echo "Exit code của lệnh test: $?"
            echo "Shell Basics hoàn thành"
        '''
    }
}

        stage('Build') {
            steps {
                echo 'Đang tạo sản phẩm Build'

                sh '''
                    mkdir -p output

                    MESSAGE=$(paste -sd " " message.txt)

                    echo "Job=$JOB_NAME" > output/build-report.txt
                    echo "Build=$BUILD_NUMBER" >> output/build-report.txt
                    echo "Commit=$SOURCE_COMMIT" >> output/build-report.txt
                    echo "Branch=$SOURCE_BRANCH" >> output/build-report.txt
                    echo "Workspace=$WORKSPACE" >> output/build-report.txt
                    echo "Message=$MESSAGE" >> output/build-report.txt

                    cat output/build-report.txt
                '''
            }
        }

        stage('Validate') {
            steps {
                echo 'Đang kiểm tra sản phẩm'

                sh '''
                    test -f output/build-report.txt

                    test -n "$SOURCE_COMMIT"
                    test -n "$SOURCE_BRANCH"

                    grep -Eq '^Build=[0-9]+$' output/build-report.txt
                    grep -Eq '^Commit=[0-9a-f]{7,64}$' output/build-report.txt
                    grep -Eq '^Branch=.+$' output/build-report.txt
                    grep -Eq '^Message=.+$' output/build-report.txt

                    echo "Validation thành công"
                '''
            }
        }

       stage('Archive') {
    steps {
        echo 'Đang lưu Artifact'

        archiveArtifacts(
            artifacts: 'output/*.txt',
            fingerprint: true
        )
    }
}
    }

    post {
        success {
            echo 'Pipeline đã thành công'
        }

        failure {
            echo 'Pipeline đã thất bại'
        }

        always {
            echo 'Pipeline đã kết thúc'
        }
    }
}
