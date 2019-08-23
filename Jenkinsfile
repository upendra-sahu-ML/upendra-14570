pipeline {
    agent {
      label "jenkins-jx-base"
    }
    environment {
      ORG               = 'upendrasahu'
      APP_NAME          = 'upendra-14570'
      CHARTMUSEUM_CREDS = credentials('jenkins-x-chartmuseum')
    }
    stages {
      stage('Git Release') {
        when {
          branch env.BRANCH_NAME
        }
        steps {
          container('jx-base') {
            // ensure we're not on a detached head
            sh "git checkout ${env.BRANCH_NAME}"
            sh 'git config --global credential.username upendrasahu'
            sh "git config --global credential.helper store"
            sh "jx step validate --min-jx-version 1.1.73"
            sh "jx step git credentials"
            // so we can retrieve the version in later steps
            sh "echo \$(jx-release-version) > VERSION"
          }
          dir ('./charts/upendra-14570') {
            container('jx-base') {
              sh "make tag"
            }
          }
        }
      }
      stage('Chart Release') {
        when {
          branch env.BRANCH_NAME
        }
        steps {
          dir ('./charts/upendra-14570') {
            container('jx-base') {
              // release the helm chart
              sh 'jx step helm release'
            }
          }
        }
      }
    }
    post {
        always {
            cleanWs()
        }
    }
  }
