node('pierre'){
    
    def app
    //def images = ["varnish", "haproxy", "mysql57-bv", "php72-bv"]
    def images = ["haproxy", "mysql57-bv", "php72-bv", "php73-bv", "php74-bv", "varnish", "sonarqube"]

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
            app.inside { 
              sh 'ls -la /'
            }
          }
          stage('Push to repo'){
            docker.withRegistry('https://registry.benvon.net','docker-publisher'){
              app.push("autobuild-${env.BUILD_NUMBER}")
            }
          }
          stage('Scan with Anchore'){
           // skip scanner
           // writeFile file: 'scanme', text: "registry.benvon.net/${buildimage}:autobuild-${env.BUILD_NUMBER}"
           // anchore annotations: [[key: 'imageType', value: "${buildimage}"]], autoSubscribeTagUpdates: false, name: 'scanme'
           // sh 'rm scanme'
            docker.withRegistry('https://registry.benvon.net','docker-publisher'){
              app.push("latest")
            }
          }
	  stage('clean up local'){
            script {
	      sh "docker image rm registry.benvon.net/${buildimage}:autobuild-${env.BUILD_NUMBER}"
	      sh "docker image rm registry.benvon.net/${buildimage}:latest"
              sh "docker image rm ${buildimage}:latest"
            }
	  }	
        }
}
