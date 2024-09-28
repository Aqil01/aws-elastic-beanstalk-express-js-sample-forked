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
        withCredentials([string(credentialsId: 'snyk-token', variable: 'SNYK_TOKEN')]) 
        script {
          def result = snykSecurity(
            snykInstallation: 'Snyk',
            failOnIssues: true,
            failOnError: true
          )
          if (result.issuesFound) {
            error "Snyk found security vulnerabilities"
          }
        }
      }
    }

    stage('Deploy') {
      steps {
        echo 'Deploying...'
        sh 'node app.js &'
        sh 'sleep 5' // Give the app some time to start
        sh 'curl http://localhost:8082' // Test if the app is running
      }
    }
  }
  
  post {
    always {
      archiveArtifacts artifacts: 'npm-debug.log', allowEmptyArchive: true
    }
  }
}
