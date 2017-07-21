pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        echo 'waiting 6 seconds ...'
        sleep(unit: 'SECONDS', time: 6)
        git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'pipelineEditorBranch', changelog: true)
        timestamps()
      }
    }
    stage('Abort if stuck') {
      steps {
          if (!continueBuild) {
            currentBuild.result = 'ABORTED'
            error('Stopping early…')
        }
      }
    }
    stage('Build') {
      steps {
        echo 'building'
        sh 'mvn install'
      }
    }
    stage('Report') {
      steps {
        echo 'Reporting'
        junit(testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true, healthScaleFactor: 1)
      }
    }
  }
}
