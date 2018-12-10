pipeline {
    agent {
        label 'docker && t2large'
    }
    environment {
        CI = 'true'
        ecr = "587764000924.dkr.ecr.us-east-1.amazonaws.com/"
    }
    stages {
        stage('Docker Build') {
            when {
                branch 'master'
            }
            steps {
                    sh "curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \ sudo apt-key add - distribution=$(. /etc/os-release;echo $ID$VERSION_ID)"
                    sh "curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \ sudo tee /etc/apt/sources.list.d/nvidia-docker.list"
                    sh "sudo apt-get update"
                    sh "sudo apt-get install -y nvidia-docker2"
                    sh "sudo pkill -SIGHUP dockerd"
                
                    sh "docker build -t interconnector -f Dockerfile.interconnect ."
                    sh "docker tag interconnector ${ecr}interconnector:${env.BUILD_ID}"
                    sh "docker tag interconnector ${ecr}interconnector:latest"


            }
        }
        stage('Docker Push') {
            when {
                branch 'master'
            }
            steps {
                sh "\$(aws ecr get-login --no-include-email --region us-east-1)"

                sh "docker push ${ecr}interconnector:${env.BUILD_ID}"
                sh "docker push ${ecr}interconnector:latest"

            }
        }
    }
}
