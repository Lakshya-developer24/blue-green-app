pipeline {
  agent any

  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t myapp:v2 .'
      }
    }

    stage('Deploy Green') {
      steps {
        sh '''
        ssh -o StrictHostKeyChecking=no ubuntu@13.53.43.155 \
        "kubectl apply -f ~/blue-green-app/deployment-green.yaml"
        '''
      }
    }

    stage('Switch Traffic') {
      steps {
        input "Switch to Green?"
        sh '''
        ssh ubuntu@13.53.43.155 \
        "kubectl patch service myapp-service -p '{\"spec\":{\"selector\":{\"app\":\"myapp\",\"color\":\"green\"}}}'"
        '''
      }
    }
  }
}
