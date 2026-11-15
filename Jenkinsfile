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
