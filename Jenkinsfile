pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        echo 'waiting 6 seconds ...'
        echo '${$VERSION}'
        sleep(unit: 'SECONDS', time: 6)
        git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'pipelineEditorBranch', changelog: true)
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
      }
    }
    stage('Build') {
      steps {
        echo 'building'
        sh 'mvn install'
        echo '$VERSION'
      }
    }
    stage('Report') {
      steps {
        echo 'Reporting'
        junit(testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true, healthScaleFactor: 1)
      }
    }
  }
  tools {
    maven 'Maven 3.3.9'
    jdk 'jdk8'
  }
  environment {
    VERSION = 'readMavenPom().getVersion()'
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    timeout(time: 60, unit: 'MINUTES')
  }
}