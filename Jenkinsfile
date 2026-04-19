pipeline {
  agent any

  stages {

    stage('Build Docker Image') {
    steps {
        sh '''
        export PATH=/usr/local/bin:$PATH
        docker build -t myapp:v2 .
        '''
    }
}

    stage('Deploy Green') {
    steps {
        sshagent(['ec2-key']) {
            sh '''
            ssh -o StrictHostKeyChecking=no ubuntu@13.53.43.155 "
            minikube start;
            kubectl config use-context minikube;
            kubectl apply -f ~/blue-green-app/deployment-green.yaml
            "
            '''
        }
    }
}

   stage('Switch Traffic') {
    steps {
        input "Switch to Green?"
        sshagent(['ec2-key']) {
            sh '''
ssh -o StrictHostKeyChecking=no ubuntu@13.53.43.155 \
'kubectl patch service myapp-service -p '"'"'{"spec":{"selector":{"app":"myapp","color":"green"}}}'"'"''
'''
        }
    }
}
  }
}
