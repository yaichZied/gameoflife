def sendMessage = {color, specificMessage->
    // Print a message to the console and to Slack.
    header = "Job <${env.JOB_URL}|${env.BRANCH_NAME}> <${env.JOB_DISPLAY_URL}|(Blue)>"
    header += " build <${env.BUILD_URL}|${env.BUILD_DISPLAY_NAME}> <${env.RUN_DISPLAY_URL}|(Blue)>:"
    message = "${header}\n${specificMessage}"
    if (lastCommit.equals(ancestorCommit)) {
        // Get last commit if we do not have a distinct ancestor.
        commitHashes = [sh(script: "git log -1 --pretty=%H", returnStdout: true).trim()]
    } else {
        // Get max 5 commits since ancestor.
        commitHashes = sh(script: "git rev-list -5 ${ancestorCommit}..", returnStdout: true).trim().tokenize('\n')
    }
    for (commit in commitHashes) {
        author = sh(script: "git log -1 --pretty=%an ${commit}", returnStdout: true).trim()
        commitMsg = sh(script: "git log -1 --pretty=%B ${commit}", returnStdout: true).trim()
        message += " Commit by <@${author}> (${author}): ``` ${commitMsg} ``` "
    }
    echo "Message ${message}"

    /* (optional snippet)
    // Send a Slack message. (Note that you need to configure a Slack access token in the Jenkins system settings).
    slackSend channel: 'yourchannelid', teamDomain: 'yourdomain', color: color, message: message, failOnError: true
    */
}

pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        timeout(time: 3, unit: 'MINUTES') {
          script {
                sendMessage '#00CC00', 'successful :smiley:'
            }
          echo 'waiting 2 seconds ...'
          sleep(unit: 'SECONDS', time: 6)
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
        
        slackSend(message: 'RUN_DISPLAY_URL : STARTED: Job ${env.JOB_NAME} [${env.BUILD_NUMBER}] (${env.RUN_DISPLAY_URL})', color: '#FFFF00', channel: 'jenkinsbuilds')
      }
    }
    stage('Build') {
      steps {
        sh 'mvn clean package '
        sh 'echo "VERSION = $VERSION"'
        sh 'mvn install'
        slackSend(message: '"STARTED: Job ${env.JOB_NAME} [${env.BUILD_NUMBER}] (${env.BUILD_URL})"', channel: 'jenkinsbuilds', color: '#FFFF00')
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
            echo "ops"
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
    timeout(time: 60, unit: 'MINUTES')
  }
}
