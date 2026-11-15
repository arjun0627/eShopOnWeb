pipeline {
    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Restore') {
            steps {
                sh 'dotnet restore eShopOnWeb.sln --disable-parallel'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build eShopOnWeb.sln --configuration Release --no-restore -m:1'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonarcloud-token', variable: 'SONAR_TOKEN')]) {
                    sh '''
                    ~/.dotnet/tools/dotnet-sonarscanner begin \
                    /k:"arjun0627_eShopOnWeb" \
                    /o:"arjun0627" \
                    /d:sonar.token="$SONAR_TOKEN" \
                    /d:sonar.host.url="https://sonarcloud.io"

                    dotnet build eShopOnWeb.sln --configuration Release --no-restore

                    ~/.dotnet/tools/dotnet-sonarscanner end \
                    /d:sonar.token="$SONAR_TOKEN"
                    '''
                }
            }

            post {
                success {
                    echo 'SonarCloud Analysis Passed'
                }

                failure {
                    echo 'SonarCloud Analysis Failed'
                }
            }
        }

        stage('Package') {
            steps {
                sh '''
                cd src/Web/bin/Release/net8.0
                zip -r /tmp/app-v2.zip .
                '''
            }
        }

        stage('Upload to S3') {
            steps {
                sh '''
                aws s3 cp /tmp/app-v2.zip s3://eshop-demo-artifacts-nagarjun/
                '''
            }
        }

        // stage('Test') {
        //     steps {
        //         sh 'dotnet test eShopOnWeb.sln --configuration Release --no-build --no-restore'
        //     }
        // }

    }

    post {
        success {
            echo 'Build Successful'
        }

        failure {
            echo 'Build Failed'
        }
    }
}
