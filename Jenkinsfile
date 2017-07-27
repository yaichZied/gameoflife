pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        parallel(
          "Initialize": {
            echo 'waiting 6 seconds ...'
            sleep(unit: 'SECONDS', time: 6)
            git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'pipelineEditorBranch', changelog: true)
            sh '''
                    echo "PATH = ${PATH}"
                    echo "JENKINS_HOME = ${JENKINS_HOME}"
                    echo "M2_HOME = ${M2_HOME}"
echo "VERSION = ${VERSION}"
                              '''
            sh '''mvn 'clean'
echo "$JENKINS_HOME"
'''
            sh 'echo " JENKINS_HOME = ${JENKINS_HOME}"'
            sh 'env'
            input 'Should I Continue ?'
            
          },
          "testing": {
            sh '''touch envVars.properties.groovy
echo  RELEASE_VERSION=$(echo $VERSION | cut -c1-$(($(echo $VERSION | grep -b -o SNAPSHOT | awk 'BEGIN {FS=":"}{print $1}') - 1))) > envVars.properties.groovy'''
            load 'envVars.properties.groovy'
            readFile 'envVars.properties.groovy'
            script {
              withEnv(['REALEASE_VERSION = load \'envVars.properties.groovy\'']) {
                echo " $RELEASE_VERSION"
                env.RELEASE_VERSION= "$RELEASE_VERSION"
                sh 'env'
              }
            }
            
            
          }
        )
      }
    }
    stage('Build') {
      steps {
        sh 'mvn install'
        sh 'echo "VERSION = $VERSION"'
        sh 'mvn clean package '
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
        
      }
    }
    stage('Report') {
      steps {
        junit(testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true, healthScaleFactor: 1)
        archiveArtifacts(artifacts: '**/target/*.jar', fingerprint: true)
        input 'Does this Build seems OK ?'
      }
    }
    stage('Delivery') {
      steps {
        sh '''touch envVars.properties
echo RELEASE_VERSION=$(echo $VERSION | cut -c1-$(($(echo $VERSION | grep -b -o SNAPSHOT | awk 'BEGIN {FS=":"}{print $1}') - 1))) > envVars.properties'''
      }
    }
  }
  tools {
    maven 'Maven 3.3.9'
    jdk 'jdk8'
  }
  environment {
    VERSION = readMavenPom().getVersion()
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    timeout(time: 60, unit: 'MINUTES')
  }
}