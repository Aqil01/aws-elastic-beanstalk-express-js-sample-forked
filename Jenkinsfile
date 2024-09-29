pipeline {
  agent {
    docker { image 'node:16' }
  }
  environment {
      SNYK_API_TOKEN = credentials('snyk_api_token')  
  }  
  stages {

    stage('Build') {
        steps {
            echo 'Installing dependencies...'
            sh 'npm install --save'
            sh 'npm audit fix'
          	sh 'npm install -g snyk'
		    	  sh 'snyk auth ${SNYK_API_TOKEN}'
        }
    }

    stage('Test') {
      steps {
        echo 'Testing...'
        sh 'snyk test --org=Aqil01 --project-name=21875438_Project2_pipeline --severity-threshold=critical'
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
