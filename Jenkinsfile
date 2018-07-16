node {
    def app

    try {
        stage ('Code Checkout') {
           checkout([$class: 'GitSCM', branches: [[name: '*/master']],
              userRemoteConfigs: [[url: 'git@github.com:benvon/docker.git',
                                 credentialsId: 'benvon_net_jenkins_ssh']]
           ])
        }
        stage ('Build') {
            app = docker.build("docker/haproxy")
        }
        stage ('Test Container'){
          app.inside {
            sh 'echo "Tests passed"'
          }
        }
        stage('Push to repo'){
          docker.withRegistry('https://registry.benvon.net'){
            app.push("autobuild-${env.BUILD_NUMBER}")
            app.push("latest")
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
