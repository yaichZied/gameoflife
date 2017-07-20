pipeline {
  agent any
  stages {
    stage('Initialize') {
      steps {
        echo 'waiting 60 seconds ...'
        sleep(unit: 'MINUTES', time: 1)
        sh '''export RELEASE_VERSION=$(echo $POM_VERSION | cut -c1-$(($(echo $POM_VERSION | grep -b -o SNAPSHOT | awk 'BEGIN {FS=":"}{print $1}') - 1)))
 
##############to be deleted when the support fix maven env variable##############
echo search maven variable env before export
printenv | grep maven
export M2_HOME=/usr/share/maven
export M2=/usr/share/maven/bin
echo search maven variable env after export
printenv | grep maven
#######################'''
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