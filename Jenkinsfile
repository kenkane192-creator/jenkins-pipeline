pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Đang tải source code từ GitHub'
                checkout scm
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

        stage('Build') {
            steps {
                echo 'Đang tạo sản phẩm Build'

                sh '''
                    mkdir -p output

                    echo "Job=$JOB_NAME" > output/build-report.txt
                    echo "Build=$BUILD_NUMBER" >> output/build-report.txt
                    echo "Commit=$GIT_COMMIT" >> output/build-report.txt
                    echo "Workspace=$WORKSPACE" >> output/build-report.txt
                    echo "Message=$(cat message.txt)" >> output/build-report.txt
                    date >> output/build-report.txt

                    cat output/build-report.txt
                '''
            }
        }

        stage('Validate') {
            steps {
                echo 'Đang kiểm tra sản phẩm'

                sh '''
                    test -f output/build-report.txt
                    grep -q "Build=" output/build-report.txt
                    grep -q "Commit=" output/build-report.txt
                    grep -q "Message=" output/build-report.txt
                '''
            }
        }

        stage('Archive') {
            steps {
                echo 'Đang lưu Artifact'

                archiveArtifacts(
                    artifacts: 'output/build-report.txt',
                    fingerprint: true
                )
            }
        }
    }

    post {
        success {
            echo 'GitHub Pipeline đã thành công'
        }

        failure {
            echo 'GitHub Pipeline đã thất bại'
        }

        always {
            echo 'Pipeline đã kết thúc'
        }
    }
}
