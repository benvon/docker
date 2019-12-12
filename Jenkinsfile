// Global vars:
//images = ["haproxy", "mysql57-bv", "php72-bv", "php73-bv"]
images = ["mysql57-bv"]

pipeline {
  agent {
    label 'docker'
  }
  options {
    disableConcurrentBuilds()
    buildDiscarder(logRotator(numToKeepStr: '4'))
    lock resource: 'image-builder'
  }
  
  stages {
    stage ('build images'){
      steps {
        buildImages()
      }
    }
    stage ('clean up local'){
      steps {
        cleanLocalImages()
      }
    }
  }
}

// Functions

def buildImages() {
  for (buildimage in images){
    dir ("${buildimage}"){
      app = docker.build("${buildimage}","--no-cache .")
      app.inside {
        sh 'echo "tests passed"'
      }
      docker.withRegistry('https://registry.benvon.net'){
        app.push("autobuild-${env.BUILD_NUMBER}")
        app.push("latest")
      }
    }
  }
}

def cleanLocalImages(){
  for (buildimage in images){
    sh "docker image rm registry.benvon.net/${buildimage}:autobuild-${env.BUILD_NUMBER}"
    sh "docker image rm registry.benvon.net/${buildimage}:latest"
    sh "docker image rm ${buildimage}:latest"
  }
}



//node('pierre'){
//    
//    def app
//    //def images = ["varnish", "haproxy", "mysql57-bv", "php72-bv"]
//    def images = ["haproxy", "mysql57-bv", "php72-bv", "php73-bv"]
//
//    try {
//        stage ('Code Checkout') {
//           checkout([$class: 'GitSCM', branches: [[name: '*/master']],
//              userRemoteConfigs: [[url: 'git@github.com:benvon/docker.git',
//                                 credentialsId: 'benvon_net_jenkins_ssh']]
//           ])
//        }
//        for (buildimage in images){
//          stage ("Build ${buildimage}") {
//            dir("${buildimage}"){
//              app = docker.build("${buildimage}", "--no-cache .")
//            }
//          }
//          stage ('Test Container'){
//            app.inside {
//              sh 'echo "Tests passed"'
//            }
//          }
//          stage('Push to repo'){
//            docker.withRegistry('https://registry.benvon.net'){
//              app.push("autobuild-${env.BUILD_NUMBER}")
//              app.push("latest")
//            }
//          }
//	  stage('clean up local'){
//            script {
//	      sh "docker image rm registry.benvon.net/${buildimage}:autobuild-${env.BUILD_NUMBER}"
//	      sh "docker image rm registry.benvon.net/${buildimage}:latest"
//              sh "docker image rm ${buildimage}:latest"
//            }
//	  }	
//        }
///*
//        stage ('Tests') {
//	        parallel 'static': {
//	            sh "echo 'shell scripts to run static tests...'"
//	        },
//	        'unit': {
//	            sh "echo 'shell scripts to run unit tests...'"
//	        },
//	        'integration': {
//	            sh "echo 'shell scripts to run integration tests...'"
//	        }
//       }
//*/
//      	stage ('Deploy') {
//            sh "echo 'shell scripts to deploy to server...'"
//      	}
//    } catch (err) {
//        currentBuild.result = 'FAILED'
//        throw err
//    }
//}

