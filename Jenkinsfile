pipeline {
  agent {
    docker {
      // Large image with full OpenModelica build dependencies; lacks omc and OMPython
      image 'openmodelica/build-deps'
    }
  }
  stages {
    stage('setup') {
      steps {
        sh '''
# Install the omc package; should only take a few seconds
apt-get update
apt-get install -qy gnupg wget ca-certificates apt-transport-https sudo
echo "deb https://build.openmodelica.org/apt `lsb_release -sc`  release" > /etc/apt/sources.list.d/openmodelica.list
wget https://build.openmodelica.org/apt/openmodelica.asc -O- | apt-key add -
apt-get update
apt-get install -qy --no-install-recommends omc
'''
      }
    }
    stage('build') {
      parallel {
        stage('python2') {
          steps {
            timeout(1) {
              // OpenModelica does not like running as root
              sh 'chown -R nobody .'
              sh 'sudo -u nobody python2 setup.py build'
              sh 'sudo -u nobody python2 setup.py test'
              sh 'python2 setup.py install'
            }
          }
        }
        stage('python3') {
          steps {
            timeout(1) {
              // OpenModelica does not like running as root
              sh 'chown -R nobody .'
              sh 'sudo -u nobody python3 setup.py build'
              sh 'sudo -u nobody python3 setup.py test'
              sh 'python3 setup.py install'
            }
          }
        }
      }
    }
  }
}