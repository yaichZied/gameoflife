pipeline {
  agent any
  
    def server = Artifactory.server "SERVER_ID"
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo

  
        rtMaven.tool = "Maven-3.3.9"
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
  
        buildInfo = rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install'
  
        server.publishBuildInfo buildInfo
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
      }
    
    def server = Artifactory.server "SERVER_ID"
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo  
        rtMaven.tool = "Maven-3.3.9"
        rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
        buildInfo = rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install'
        server.publishBuildInfo buildInfo
  
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
