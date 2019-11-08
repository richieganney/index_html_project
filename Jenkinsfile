pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        sh '''mkdir -p build
cp index.html build/index.html
echo ${BUILD_NUMBER} > release.txt
cp release.txt build/release.txt'''
      }
    }
    stage('create artifacts'){
      steps {
        sh 'zip -r build.zip build'
      }
    }
    stage('archive artifacts'){
      steps {
        archiveArtifacts 'build.zip'
      }
    }
    stage('copying artifacts to remotes'){
      steps {
        sshPublisher(publishers: [sshPublisherDesc(configName: 'webserver', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'unzip build.zip', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'build.zip')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
      }
    }
    stage('moving artifacts into the right folder'){
      steps {
        script {
          sh 'ssh -tt richieganney@192.168.56.104 << EOF'
          sh 'whoami'
          sh 'cp /home/richieganney/build/* /var/www/html'
          sh 'rm -rf /home/richieganney/build'
          sh 'rm -rf /home/richieganney/build.zip'
          sh 'exit'
          sh 'EOF'
        }
      }
    }
  }
}