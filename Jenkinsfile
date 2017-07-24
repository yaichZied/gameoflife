pipeline {
  agent any
  node('vagrant-slave') {
        env.JAVA_HOME="${tool 'jdk-8u45'}"
        env.PATH="${env.JAVA_HOME}/bin:${env.PATH}"
        sh 'java -version' 
  }
  stages {
    stage('Initialize') {
      steps {
        echo 'waiting 6 seconds ...'
        sleep(unit: 'SECONDS', time: 6)
        git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'pipelineEditorBranch', changelog: true)
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
