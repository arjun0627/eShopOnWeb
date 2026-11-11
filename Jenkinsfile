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
                sh 'dotnet restore eShopOnWeb.sln'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build eShopOnWeb.sln --configuration Release --no-restore'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test eShopOnWeb.sln --configuration Release --no-build'
            }
        }
    }

    post {
        success {
            echo "Build Successful"
        }

        failure {
            echo "Build Failed"
        }
    }
}
