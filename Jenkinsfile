pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        timeout(time: 3, unit: 'MINUTES') {
          git(poll: true, url: 'https://github.com/yaichZied/gameoflife.git', branch: '${BRANCH_NAME}', changelog: true)
        }
        
        sh 'env'
      }
    }
    stage('Build & Deployment') {
      steps {
        script {
          slackSend (message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})" ,color: '#FFFF00')
          configFileProvider([configFile( fileId: 'c775a584-3f02-4ba0-bfb1-f559bc87178d', variable: 'MAVEN_SETTINGS')]) {
            sh "mvn    clean -U"
            sh "mvn  install -U  "
            sh "mvn  -s $MAVEN_SETTINGS  deploy -U"
          }
        }
        
      }
    }
    stage('SonarQube') {
      steps {
        script {
          configFileProvider([configFile( fileId: 'c775a584-3f02-4ba0-bfb1-f559bc87178d', variable: 'MAVEN_SETTINGS')]) {
            sh" mvn -s $MAVEN_SETTINGS sonar:sonar "
          }
        }
        
      }
    }
    stage('Report') {
      steps {
        junit(testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true, healthScaleFactor: 1)
        archiveArtifacts(artifacts: '**/target/*.jar', fingerprint: true)
        archiveArtifacts(artifacts: '**/target/*-infrastructure.zip', fingerprint: true)
        timeout(time: 1, unit: 'HOURS') {
          input 'Do you want proceed to the delivery ?'
        }
        
      }
    }
    stage('Deployment') {
      steps {
        configFileProvider([configFile( fileId: 'c775a584-3f02-4ba0-bfb1-f559bc87178d', variable: 'MAVEN_SETTINGS')]) {
          sh 'git clean -df && git reset --hard'
          sh '"mvn -s $MAVEN_SETTINGS release:clean release:prepare release:perform -U "'
        }
        script {
          sh "mvn dependency:get -X -DremoteRepositories=http://admin:admin123@127.0.0.1:8081/repository/maven-releases -Dartifact=com.wakaleo.gameoflife:gameoflife-web:RELEASE:war -Dtransitive=false"
        }
        
      }
    }
    stage('Docker') {
      steps {
        echo 'depoying app'
        sh '''cp /var/lib/jenkins/.m2/repository/com/wakaleo/gameoflife/gameoflife-web/${VERSION}/gameoflife-web-${VERSION}.war  ./infrastructure/environnement_recette/			

mv infrastructure/environnement_recette/*.war infrastructure/environnement_recette/gameoflife-web.war'''
        sh '''cp ./target/*-infrastructure.zip .

'''
        sh '''unzip -o -j /*-infrastructure.zip "infrastructure/environnement_recette/*"

docker-compose --project-name socle-javaee-rec -f environnement_recette.yml down -v
'''
        sh 'VERSION=${VERSION}'
        sh '''docker-compose --project-name socle-javaee-rec -f infrastructure/environnement_recette/environnement_recette.yml up --build -d
'''
      }
    }
  }
  tools {
    maven 'Maven 3.3.9'
    jdk 'jdk8'
  }
  environment {
    VERSION = readMavenPom().getVersion().replace("-SNAPSHOT","")
  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    timeout(time: 60, unit: 'MINUTES')
  }
}
