pipeline {
  agent any
  environment {
    CI = 'true'
    HOME = '.'
    npm_config_cache = 'npm-cache'
  }
  stages {
    stage('Install Packages') {
      steps {
        sh 'npm install'
      }
    }
    stage('Test and Build') {
      parallel {
        stage('Run Tests') {
          steps {
            sh 'npm run test'
          }
        }
        stage('Create Build Artifacts') {
          steps {
            sh 'npm run build'
          }
        }
      }
    }
    stage('Deployment') {
      parallel {
        stage('Staging') {
          when {
            branch 'staging'
          }
          steps {
            sh 'aws s3 sync build/ s3://front-lemontech-eys'
          }
        }
        stage('Production') {
          when {
            branch 'master'
          }
          steps {
            sh 'aws s3 sync build/ s3://front-lemontech-eys'
            sh 'aws cloudfront create-invalidation --distribution-id E4R6F0ILHGLD2 --paths "/*" '
          }
        }
      }
    }
  }
}
