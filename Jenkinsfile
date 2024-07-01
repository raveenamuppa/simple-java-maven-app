pipeline {
    agent any
    tools {
        maven 'Maven_3.9.8' 
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            cleanWs()
        }

        success {
            echo 'Build succeeded'
        }

        failure {
            echo 'Build failed'
        }
    }
}
