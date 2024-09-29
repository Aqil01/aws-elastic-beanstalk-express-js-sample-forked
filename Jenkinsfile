pipeline {
  agent {
    docker { image 'node:16' }
  }

  stages {

    stage('Build') {
        steps {
            echo 'Building...'
            echo 'Installing dependencies...'
            sh 'npm install --save'
            sh 'npm audit fix'
        }
    }

    stage('Test') {
      steps {
        echo 'Testing...'
        sh 'npm install -g snyk'
        // Directly using the Snyk token for authentication
        sh 'snyk auth b40e9c90-9ccb-4eb5-b2f8-337a2c3b26de' 
        script {
                def snykScanResult = sh(script: 'snyk test --severity-threshold=critical', returnStatus: true)
                if (snykScanResult != 0) {
                error 'Critical vulnerabilities detected by Snyk!'
                } else {
                echo 'Snyk scan completed with no critical vulnerabilities.'
                }
        }
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
  
  post {
    always {
      archiveArtifacts artifacts: 'npm-debug.log', allowEmptyArchive: true
    }
 }
}
