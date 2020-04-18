pipeline {
  agent any

  options {
    buildDiscarder(logRotator(numToKeepStr: '100'))
  }

  stages {

    // Step 1. Install Dependencies
    stage('Install Project Dependencies') {
      parallel {
        stage('install php deps') {
          steps {
            sh 'composer install'
            //create file vendor/autoload.php load rules is implemented in vendor/composer/autoload_*.php
            sh 'composer dump-auto'
          }
        }

        stage('install npm deps') {
          steps {
            sh 'npm install'
          }
        }
      }
    }

    // Step 2. Package Code into distribution tarball
    stage('Build Tarball') {
      steps {
        sh '''BRANCH="$(echo ${GIT_BRANCH})"
TREEISH="$(echo ${GIT_COMMIT} | cut -c 1-6)"
FN="daithekyv2-v${BUILD_NUMBER}-${BRANCH}@${TREEISH}.tar.xz"
echo "building ${FN}"

#build deployable archive:
tar -cJf "/app/jenkins/${FN}" -C "${WORKSPACE}" .'''
      }
    }
  }

  post {
    success {
      sh 'BRANCH="$(echo ${GIT_BRANCH})"'
      sh 'curl --header "Content-Type: application/json" \
  --request POST \
  --data \'{\"text\":"Build Successfull $BRANCH :D"}\' \
  https://outlook.office.com/webhook/b367fe3f-43da-4982-8d7c-c36a940036ee@6eee0fd9-69c6-4ab8-ab85-3dc2748b1f08/IncomingWebhook/b385d78aed484294b9e3040dac79114b/575e86a8-0eb5-4295-b06e-d1d3e773e8b6'
    }
    failure {
      sh 'curl --header \"Content-Type: application/json\" \
  --request POST \
  --data \'{\"text\":"Build Failure $BRANCH :("}\' \
  https://outlook.office.com/webhook/b367fe3f-43da-4982-8d7c-c36a940036ee@6eee0fd9-69c6-4ab8-ab85-3dc2748b1f08/IncomingWebhook/b385d78aed484294b9e3040dac79114b/575e86a8-0eb5-4295-b06e-d1d3e773e8b6'
    }
  }
}
