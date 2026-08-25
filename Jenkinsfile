pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }
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
                    artifacts: 'output/build-report.txt',
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
