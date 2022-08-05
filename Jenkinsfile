pipeline {
   agent any
    environment {
      RELEASE='21.04'
    }
   stages {
      stage('Build') {
            environment {
               LOG_LEVEL='INFO'
            }
            steps {
               echo "Building release ${RELEASE} with log level ${LOG_LEVEL}..."
               sh 'chmod +x api.sh'
               withCredentials([string(credentialsId: 'an-api-key', variable: 'API_KEY')]) {
                  sh '''
                     api.sh
                  '''
               }
            }
        }
        stage('Test') {
            steps {
               echo "Testing release ${RELEASE}"
               script {
                  if (Math.random() > 0.6) {
                     throw new Exception()
                  }
               }
               writeFile file: 'test-results.txt', text: 'passed'               
            }
        }
   }
   post {
      success {
         archiveArtifacts 'test-results.txt'
         slackSend channel: '#builds',
                   color: 'good',
                   message: "Release ${env.RELEASE}, success: ${currentBuild.fullDisplayName}."
      }
      failure {
         slackSend channel: '#builds',
                   color: 'danger',
                   message: "Release ${env.RELEASE}, FAILED: ${currentBuild.fullDisplayName}."
      }
   }
}
