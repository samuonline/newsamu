pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building the code...'
                sh 'mvn clean install'
            }
        }
        stage('Unit and Integration Tests') {
            steps {
                echo 'Running unit and integration tests...'
                sh 'mvn test'
            }
        }
        stage('Code Analysis') {
            steps {
                echo 'Performing code analysis...'
                sh 'sonar-scanner'
            }
        }
        stage('Security Scan') {
            steps {
                echo 'Running security scan...'
                sh 'dependency-check'
            }
        }
        stage('Deploy to Staging') {
            steps {
                echo 'Deploying to staging...'
                sh 'aws deploy push ...'
            }
        }
        stage('Integration Tests on Staging') {
            steps {
                echo 'Running integration tests on staging...'
                sh 'postman run tests'
            }
        }
        stage('Deploy to Production') {
            steps {
                echo 'Deploying to production...'
                sh 'aws deploy push ...'
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            mail to: 'dewumisamuduni@gmail.com',
                 subject: "Pipeline Success",
                 body: """The pipeline has succeeded successfully.
                 Check the logs for more details."""
        }
        failure {
            mail to: 'dewumisamuduni@gmail.com',
                 subject: "Pipeline Failure",
                 body: """The pipeline has failed. Please check the logs."""
        }
    }
}
