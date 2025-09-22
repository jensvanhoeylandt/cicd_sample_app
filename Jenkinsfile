node {
    stage('Preparation') {
        // Stop & remove zonder falen als er niets draait
        catchError(buildResult: 'SUCCESS', stageResult: 'SUCCESS') {
            sh 'docker stop samplerunning || true'
            sh 'docker rm samplerunning || true'
        }
    }
    stage('Build') {
        // Run je bestaande Freestyle build job
        build job: 'BuildSampleApp', propagate: true
    }
    stage('Results') {
        // Run je bestaande Freestyle test job
        build job: 'TestSampleApp', propagate: true
    }
}
