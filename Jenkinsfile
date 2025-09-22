pipeline {
  agent any
  options { timestamps() }
  triggers { pollSCM('* * * * *') }
  stages {
    stage('Preparation') {
      steps {
        sh '''
          docker stop samplerunning || true
          docker rm samplerunning || true
          git clean -fdx || true
        '''
      }
    }
    stage('Build') {
      steps {
        build job: 'BuildSampleApp', propagate: true
      }
    }
    stage('Test') {
      steps {
        build job: 'TestSampleApp', propagate: true
      }
    }
  }
  post {
    always {
      sh 'docker ps --format "table {{.Names}}\\t{{.Image}}\\t{{.Status}}\\t{{.Ports}}" || true'
    }
  }
}
