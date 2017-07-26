pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        echo 'waiting 6 seconds ...'
        sleep(unit: 'SECONDS', time: 6)
        git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'pipelineEditorBranch', changelog: true)
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                '''
        sh 'mvn \'clean\''
        sh 'echo " VERSION = ${VERSION}"'
        sh 'env'
      }
    }
    stage('Build') {
      steps {
        sh 'mvn install'
        sh 'echo "VERSION = $VERSION"'
      }
    }
    stage('Report') {
      steps {
        echo 'Reporting'
        junit(testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true, healthScaleFactor: 1)
        archiveArtifacts(artifacts: '**/target/*.jar', fingerprint: true)
      }
    }
    stage('SonarQube') {
      steps {
        script {
          scannerHome = tool 'sonar'
          withSonarQubeEnv('sonarServer') {
            sh "${scannerHome}/bin/sonar-scanner"
          }
        }
        
        timeout(time: 2, unit: 'MINUTES') {
       
            def qg = waitForQualityGate()
            if (qg.status == 'OK') {
              error "Pipeline aborted due to quality gate failure: ${qg.status}"
          }
          
        }
        
        echo 'clear'
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
