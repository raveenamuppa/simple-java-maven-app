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
             def artifactPath = sh(script: 'ls target/*.jar', returnStdout: true).trim()
             def remotePath = '/home/ec2-user/your-app'
             
             sh "ssh -i ${sshKeyPath} ${ec2User}@${ec2Host} 'mkdir -p ${remotePath}'"
             sh "scp -i ${sshKeyPath} ${artifactPath} ${ec2User}@${ec2Host}:${remotePath}"

             def remoteCommands = """
            
                 cd ${remotePath}
                 pkill -f my-app-1.0-SNAPSHOT.jar || true
                 nohup java -jar -Dspring.profiles.active=prod my-app-1.0-SNAPSHOT.jar > output.log 2>&1 &
             """
             sh "ssh -i ${sshKeyPath} ${ec2User}@{ec2Host} << 'EOF'\n${remoteCommands}\nEOF"
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
