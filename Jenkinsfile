pipeline {
  agent {
    docker { image 'node:16' }
  }
  stages {

    stage('Build') {
        steps {
            echo 'Installing dependencies...'
            sh 'npm install --save'
            sh 'npm audit fix'
        }
    }

    stage('Test') {
      steps {
        echo 'Testing...'
        snykSecurity(
          snykInstallation: 'Snyk',
          snykTokenId: 'b40e9c90-9ccb-4eb5-b2f8-337a2c3b26de',
          failOnIssues: true,
          failOnError: true
        )
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deploying...'
        sh 'node app.js &'
        sh 'sleep 5' // Give the app some time to start
        sh 'curl http://localhost:8082'
      }
    }
  }
  
 // post {
  //  always {
 //     archiveArtifacts artifacts: 'npm-debug.log', allowEmptyArchive: true
 //   }
 // }
}
