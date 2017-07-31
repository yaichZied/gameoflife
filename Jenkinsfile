pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        timeout(time: 3, unit: 'MINUTES') {
          echo 'waiting 2 seconds ....'
          sleep(unit: 'SECONDS', time: 2)
          git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: 'BranchPipelineEditor', changelog: true)
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
        }
        
      }
    }
    stage('Build') {
      steps {
        script {
          emailext (
            subject: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
            body: """<p>STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
            <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>"</p>""",
            recipientProviders: [[$class: 'DevelopersRecipientProvider']]
          )
          slackSend (message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})" ,color: '#FFFF00')
        }
        
        sh 'mvn clean package '
        sh 'echo "VERSION = $VERSION"'
        sh 'mvn install'
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
        sh '''touch envVars.properties.groovy
echo  RELEASE_VERSION=$(echo $VERSION | cut -c1-$(($(echo $VERSION | grep -b -o SNAPSHOT | awk 'BEGIN {FS=":"}{print $1}') - 1))) > envVars.properties.groovy'''
        load 'envVars.properties.groovy'
        script {
          withEnv(['REALEASE_VERSION = load \'envVars.properties.groovy\'']) {
            echo " $RELEASE_VERSION"
            env.RELEASE_VERSION= "$RELEASE_VERSION"
            
          }
        }
        
      }
    }
    stage('Deployement') {
      steps {
        echo 'depoying'
        script {
          configFileProvider([configFile('c775a584-3f02-4ba0-bfb1-f559bc87178d')]) {
            echo "settings.xml"
          }
        }
        
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
    timeout(time: 6, unit: 'MINUTES')
  }
  triggers {
    cron('* * * * *')
  }
}