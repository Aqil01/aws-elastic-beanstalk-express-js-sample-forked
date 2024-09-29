pipeline {
  agent {
    docker { 
		image 'node:16' 
		//args '-p 8082:8082' //binding port 8082 from app.js in docker to localhost:8082
	} 
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
	echo 'Initiating Snyk security scan for vulnerabilities...'
        sh 'npm install -g snyk'
	// Directly using the Snyk token for authentication
        sh 'snyk auth b40e9c90-9ccb-4eb5-b2f8-337a2c3b26de'
        echo 'Snyk authentication successful.'
        script {
        def snykScanResult = sh(script: 'snyk test --severity-threshold=high', returnStatus: true)
        if (snykScanResult != 0) {
        error 'Critical vulnerabilities detected by Snyk! Failing the pipeline.'
        } else {
        echo 'Snyk scan completed with no critical vulnerabilities. Proceeding...'
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
      echo 'Pipeline execution completed'
    }
    failure {
      echo 'Pipeline has failed due to errors, please check logs'
  }
    success {
      echo 'Pipeline executed successfully without any errors'
}
  }
}
