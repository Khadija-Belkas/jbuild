def pipelineContext = [:]
node {

   def registryProjet='registry.gitlab.com/xavki/presentations-jenkins'
	 def IMAGE="${registryProjet}:version-${env.BUILD_ID}"

	 echo "IMAGE = $IMAGE"

    stage('Clone') {
    			checkout scm
		}

		def img = stage('Build') {
					docker.build("$IMAGE",  '.')
		}
	
		stage('Run') {
			steps {
    			bat 'docker stop run-9 || exit 0'
    			bat 'docker rm run-9 || exit 0'
    			bat "docker run -d --name run-app -p 9000:80 ${IMAGE}"
          			}					
		}

		stage('Push') {
					docker.withRegistry('https://registry.gitlab.com', 'reg1') {
							img.push 'latest'
             					 img.push()
					}
		}
 
}

