 @Library('dynatrace@v1.0') _

def tagMatchRules = [
  [
    meTypes: [
      [meType: 'SERVICE']
    ],
    tags : [
      [context: 'CONTEXTLESS', key: 'app', value: 'carts'],
      [context: 'CONTEXTLESS', key: 'environment', value: 'dev']
    ]
  ]
]

pipeline {
  agent {
    label 'maven'
  }
  environment {
    APP_NAME = "webapp"
    VERSION = readFile('version').trim()
    ARTEFACT_ID = "sockshop/" + "${env.APP_NAME}"
    TAG = "${env.DOCKER_REGISTRY_URL}:5000/library/${env.ARTEFACT_ID}"
    TAG_DEV = "${env.TAG}-${env.VERSION}-${env.BUILD_NUMBER}"
    TAG_STAGING = "${env.TAG}-${env.VERSION}"
  }
  stages {
    stage('Docker build') {
      when {
        expression {
          return env.BRANCH_NAME ==~ 'release/.*' || env.BRANCH_NAME ==~'master'
        }
      }
      steps {
        container('docker') {
          sh "docker build -t ${env.TAG_DEV} ."
		  sh "docker images" 
        }
      }
    }
    stage('Docker push to registry'){
      when {
        expression {
          return env.BRANCH_NAME ==~ 'release/.*' || env.BRANCH_NAME ==~'master'
        }
      }
      steps {
        container('docker') {
          sh "docker push ${env.TAG_DEV}"
		  //sh "docker pull domuharahap/web_app:latest"
		  //sh "docker tag domuharahap/web_app:latest ${env.TAG_DEV}"
		  //sh "docker push ${env.TAG_DEV}"
        }
      }
    }
    stage('Deploy to dev namespace') {
      when {
        expression {
          return env.BRANCH_NAME ==~ 'release/.*' || env.BRANCH_NAME ==~'master'
        }
      }
      steps {
        container('kubectl') {
      //    sh "sed -i 's#image: .*#image: ${env.TAG_DEV}#' manifest/carts.yml"
      //    sh "kubectl -n dev apply -f manifest/carts.yml"
			sh "kubectl run webapp --image ${env.TAG_DEV}"
        }
      }
    }
    
  }
}
