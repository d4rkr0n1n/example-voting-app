pipeline {
    agent any
    parameters {
        string(name: 'APP_IP', defaultValue: '10.100.4.216')
    }
    stages {
        stage('Build') {
            steps{
                sshagent(['872edeb0-f9bc-47b3-9d08-8f6a5a3a4430']){
                    sh '''
                    echo "${params.APP_IP}"
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.3.44 "aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 087424291777.dkr.ecr.ap-south-1.amazonaws.com"
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.3.44 "rm -rf /home/ubuntu/vote/"
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
        stage('Deploy') {
            steps{
                sshagent(['872edeb0-f9bc-47b3-9d08-8f6a5a3a4430']){
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.4.216 "docker pull 087424291777.dkr.ecr.ap-south-1.amazonaws.com/vote-app:latest"
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.4.216 "docker rm -f vote-app"
                    ssh -o StrictHostKeyChecking=no ubuntu@10.100.4.216 "docker run -dit -p 8080:80 --name vote-app 087424291777.dkr.ecr.ap-south-1.amazonaws.com/vote-app:latest"
                    '''
                }
            }
        }
    }
}
