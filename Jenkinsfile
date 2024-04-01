pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        parallelsAlwaysFailFast()
        timestamps()
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '6')
        skipDefaultCheckout(true)
    }

    stages {
        stage('Checkout SCM') {
            steps {
                cleanWs()
                checkout scm
            }
        }
        stage('Execute Nexial Command') {
            steps {
                // Execute Nexial command
                bat "nexial -script TN5250\\artifact\\script\\TN5250.xlsx"
            }
        }
    }
    post {
        always {
            withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'aws-creds', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                bat "7z a output-${BUILD_NUMBER}.zip TN5250\\output\\**\\"
                bat "aws s3 cp output-${BUILD_NUMBER}.zip s3://as400-screen-captures"
                   
            }
        }
    }
}
