pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        echo 'Initializing'
        timeout(unit: 'DAYS', time: 50)
        deleteDir()
      }
    }
    stage('Build') {
      steps {
        echo 'building'
      }
    }
    stage('Report') {
      steps {
        echo 'Reporting'
      }
    }
  }
}