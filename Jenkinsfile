pipeline {
    agent any

    environment {
        IMAGE = "dhanushdock1/mysite:${BUILD_NUMBER}"
        CONTAINER = "mysite"
        SERVER = "ec2-user@51.20.181.210"
    }

    stages {

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no $SERVER "
                        docker pull $IMAGE &&
                        docker stop $CONTAINER || true &&
                        docker rm $CONTAINER || true &&
                        docker run -d -p 80:80 --name $CONTAINER $IMAGE
                    "
                    '''
                }
            }
        }
    }
}
