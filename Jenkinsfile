pipeline {
  agent {
    docker { image 'node:16'
             args '-p 8082:8082' //binding port 8082 from app.js in docker to localhost:8082
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
        script {
          def result = snykSecurity(
            snykInstallation: 'Snyk',
            snykTokenId: 'b40e9c90-9ccb-4eb5-b2f8-337a2c3b26de',
            failOnIssues: true,
            failOnError: true
          )
          if (result.issuesFound) {
            echo 'Snyk found security vulnerabilities'
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
