pipeline {
//Specifies that the pipeline will run in a Docker container
  agent {
    docker { image 'node:16' } //use Node.js 16 as the base image
  }

  stages {
//Build Stage
    stage('Build') {
        steps {
            echo 'Building...'
            echo 'Installing dependencies...'
            sh 'npm install --save' //install project dependencies
            sh 'npm audit fix' //Attempt to fix any vulnerabilities in dependencies
        }
    }
//Test Stage
    stage('Test') {
      steps {
        echo 'Testing...'
        sh 'npm install -g snyk' //install Snyk globally
        // Directly using the Snyk token for authentication simplicity
        sh 'snyk auth b40e9c90-9ccb-4eb5-b2f8-337a2c3b26de' 
        script {
                def snykScanResult = sh(script: 'snyk test --severity-threshold=critical', returnStatus: true)
                if (snykScanResult != 0) {
                error 'Critical vulnerabilities detected by Snyk!' //fail the build if critical vulnerabilities are found
                } else {
                echo 'Snyk scan completed with no critical vulnerabilities.'
                }
        }
      }
    }
//Deploy Stage
    stage('Deploy') {
      steps {
        echo 'Deploying...'
        sh 'node app.js &' //start the node.js app in the background
        sh 'sleep 5' // Give the app some time to start
        sh 'curl http://localhost:8082' //send req to application to verify it is running
      }
    }
  }
//Post build action  
  post {
    always {
//archive the npm debug log
      archiveArtifacts artifacts: 'npm-debug.log', allowEmptyArchive: true
    }
 }
}
