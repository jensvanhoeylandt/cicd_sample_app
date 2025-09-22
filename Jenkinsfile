pipeline {
  agent any
  options {
    timestamps()
    ansiColor('xterm')
  }
  triggers {
    pollSCM('* * * * *')
  }
  environment {
    APP_NAME = 'sampleapp'
    RUN_NAME = 'samplerunning'
    PORT     = '5050'
  }
  stages {
    stage('Preparation') {
      when { branch 'main' }
      steps {
        sh '''
          set -e
          docker stop "$RUN_NAME" 2>/dev/null || true
          docker rm "$RUN_NAME" 2>/dev/null || true
          git clean -fdx || true
        '''
      }
    }
    stage('Build') {
      when { branch 'main' }
      steps {
        sh '''
          set -e
          docker build -t "$APP_NAME" .
        '''
      }
    }
    stage('Test') {
      when { branch 'main' }
      steps {
        sh '''
          set -e
          APP_IP=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' "$RUN_NAME" 2>/dev/null || true)
          [ -n "$APP_IP" ] || {
            docker run -d --name "$RUN_NAME" -p ${PORT}:5050 "$APP_NAME"
            sleep 2
          }
          JENKINS_IP=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' jenkins_server)
          RESP=$(curl -s "http://localhost:${PORT}/")
          echo "$RESP"
          echo "$RESP" | grep "You are calling me from ${JENKINS_IP}"
        '''
      }
    }
    stage('Deploy') {
      when { branch 'main' }
      steps {
        sh '''
          set -e
          docker stop "$RUN_NAME" 2>/dev/null || true
          docker rm "$RUN_NAME" 2>/dev/null || true
          docker run -d --name "$RUN_NAME" -p ${PORT}:5050 "$APP_NAME"
        '''
      }
    }
  }
  post {
    always {
      sh 'docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}\t{{.Ports}}" || true'
      echo "Klaar. Check: http://192.168.56.20:${PORT}/"
    }
  }
}
