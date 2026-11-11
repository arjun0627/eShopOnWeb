pipeline {

    agent any

    options {
        timestamps()
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        DOTNET_SKIP_FIRST_TIME_EXPERIENCE = '1'
    }

    stages {

        stage('Display Branch') {
            steps {
                echo "Branch Name : ${env.BRANCH_NAME}"
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Workspace Info') {
            steps {
                sh '''
                    pwd
                    ls -ltr
                '''
            }
        }

        stage('Restore') {
            steps {
                sh '''
                    dotnet restore
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                    dotnet build --configuration Release --no-restore
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    dotnet test --configuration Release --no-build
                '''
            }
        }

        stage('Publish') {
            steps {
                sh '''
                    mkdir -p publish
                    dotnet publish --configuration Release -o publish
                '''
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'publish/**', fingerprint: true
            }
        }
    }

    post {

        success {
            echo "Build Successful : ${env.BRANCH_NAME}"
        }

        failure {
            echo "Build Failed : ${env.BRANCH_NAME}"
        }

        always {
            cleanWs()
        }
    }
}
