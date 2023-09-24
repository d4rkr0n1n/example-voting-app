pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps{
                sshagent(['872edeb0-f9bc-47b3-9d08-8f6a5a3a4430']){
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.3.44 "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 087424291777.dkr.ecr.ap-south-1.amazonaws.com"
                    scp -r vote/ ubuntu@10.100.3.44:/home/ubuntu/vote
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.3.44 "docker build -t vote-app /home/ubuntu/vote/"
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.3.44 "docker tag vote-app:latest 087424291777.dkr.ecr.ap-south-1.amazonaws.com/vote-app:latest"
                    '''
                }
            }
        }
        stage('Push to ECR') {
            steps{
                sshagent(['872edeb0-f9bc-47b3-9d08-8f6a5a3a4430']){
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.3.44 "docker push 087424291777.dkr.ecr.ap-south-1.amazonaws.com/vote-app:latest"
                    '''
                }
            }
        }
    }
}
