pipeline {
  agent any
  
  stages {
    stage('Initialize') {
      steps {
        echo 'waiting 6 seconds ...'
        sleep(unit: 'SECONDS', time: 6)
        git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'non-functional_pipeline', changelog: true)
      }
    }
    stage('SonarQube analysis') {
      steps {

  withSonarQubeEnv {
    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.3.0.603:sonar ' +
            '-Dsonar.projectKey=Nimbu'+
            '-Dsonar.projectName=Nimbu'+
            '-Dsonar.projectVersion=1.0'+
            '-Dsonar.sources= /var/lib/jenkins/workspace/$JOB_NAME'
        }
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
