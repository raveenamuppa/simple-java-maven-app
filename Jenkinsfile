pipeline {
    agent any
    tools {
        maven 'mvn' 
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

        stage('deploy') {
          steps {
           script {
             def ec2User = 'ec2-user'
             def ec2Host = '34.227.46.78'
             def sshKeyPath = '/Users/raveenamuppa/.ssh/app-deploy.pem'
             def artifactPath = 'target/your-artifact.jar'
             def remotePath = '/home/ec2-user/your-app'

             sh "scp -i ${sshKeyPath} ${artifactPath} ${ec2User}@${ec2Host}:${remotePath}"

             sh """
             ssh -i ${sshKeyPath} ${ec2User}@{ec2Host} << EOF
             cd ${remotePath}
             pkill -f your-artifact.jar || true
             nohup java -jar your-artifact.jar &
             EOF
             """
            }
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
