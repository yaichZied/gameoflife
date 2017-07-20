pipeline {
  agent any
  withSonarQubeEnv {
            sonar.projectKey=Nimbu
            sonar.projectName=Nimbu
            sonar.projectVersion=1.0
            sonar.sources= /var/lib/jenkins/workspace/$JOB_NAME
        }
  stages {
    stage('Initialize') {
      steps {
        echo 'waiting 6 seconds ...'
        sleep(unit: 'SECONDS', time: 6)
        git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'non-functional_pipeline', changelog: true)
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
