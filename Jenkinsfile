node('pierre'){
    
    def app
    //def images = ["varnish", "haproxy", "mysql57-bv", "php72-bv"]
    def images = ["haproxy", "mysql57-bv", "php72-bv", "php73-bv", "php74-bv"]

    try {
        stage ('Code Checkout') {
           checkout([$class: 'GitSCM', branches: [[name: '*/master']],
              userRemoteConfigs: [[url: 'git@github.com:benvon/docker.git',
                                 credentialsId: 'jenkins-github-ssh']]
           ])
        }
        for (buildimage in images){
          stage ("Build ${buildimage}") {
            dir("${buildimage}"){
              app = docker.build("${buildimage}", "--no-cache .")
            }
          }
          stage ('Test Container'){
            app.inside {
              sh 'ls -la /'
            }
            //writeFile file: 'scanme', text: "${buildimage}"
            //anchore name: 'scanme'
            //sh 'rm scanme'
          }
          stage('Push to repo'){
            docker.withRegistry('https://registry.benvon.net','docker-publisher'){
              app.push("autobuild-${env.BUILD_NUMBER}")
              app.push("latest")
            }
          }
          stage('Scan image with Anchore'){
            writeFile file: 'scanme', text: "https://registry.benvon.net/${buildimage}:autobuild-${env.BUILD_NUMBER}"
            anchore name: 'scanme'
            sh 'rm scanme'
          }
	  stage('clean up local'){
            script {
	      sh "docker image rm registry.benvon.net/${buildimage}:autobuild-${env.BUILD_NUMBER}"
	      sh "docker image rm registry.benvon.net/${buildimage}:latest"
              sh "docker image rm ${buildimage}:latest"
            }
	  }	
        }
/*
        stage ('Tests') {
	        parallel 'static': {
	            sh "echo 'shell scripts to run static tests...'"
	        },
	        'unit': {
	            sh "echo 'shell scripts to run unit tests...'"
	        },
	        'integration': {
	            sh "echo 'shell scripts to run integration tests...'"
	        }
        }
*/
      	stage ('Deploy') {
            sh "echo 'shell scripts to deploy to server...'"
      	}
    } catch (err) {
        currentBuild.result = 'FAILED'
        throw err
    }
}

