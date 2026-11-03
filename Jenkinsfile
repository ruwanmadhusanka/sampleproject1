pipeline {
  agent any
  triggers { githubPush() }
  options { disableConcurrentBuilds() }
  parameters {
    booleanParam(name: 'RELEASE', defaultValue: false, description: 'Release and publish CApp to Nexus')
    string(name: 'RELEASE_VERSION', defaultValue: '', description: 'e.g. 1.0.0')
    string(name: 'DEV_VERSION', defaultValue: '', description: 'e.g. 1.0.1-SNAPSHOT')
  }
  stages {
    stage('Build') {
      steps { sh 'mvn -B clean install -Dmaven.test.skip=true' }
    }
    stage('Release') {
      when { expression { params.RELEASE } }
      steps {
        withCredentials([
          usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN'),
          usernamePassword(credentialsId: 'nexus-credentials', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')
        ]) {
          sh '''
            git checkout -B main
            mvn -B release:prepare release:perform \
              -DreleaseVersion="$RELEASE_VERSION" \
              -DdevelopmentVersion="$DEV_VERSION" \
              -Darguments="-Dmaven.test.skip=true"
          '''
        }
      }
    }
  }
}