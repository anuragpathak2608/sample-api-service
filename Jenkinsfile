pipeline {
  agent {
    kubernetes {
      yamlFile 'build-agent.yaml'
      defaultContainer 'maven'
      idleMinutes 1
    }
  }
  environment {
    APP_NAME = "sample-api-service"
    IMAGE_REGISTRY = "rmkanda"
  }
  stages {
    stage('Setup') {
      parallel {
        stage('Install Dependencies') {
          steps {
            container('maven') {
              sh './mvnw install -DskipTests -Dspotbugs.skip=true -Ddependency-check.skip=true'
            }
          }
        }
      }
    }
    stage('DAST') {
      steps {
        container('docker-tools') {
          sh "docker pull owasp/zap2docker-weekly"
          sh "docker run -t owasp/zap2docker-stable zap-api-scan.py -t http://172.17.0.5:30001/v3/api-docs -f openapi"
        }
      }
    }
    
  }
}