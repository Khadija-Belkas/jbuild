def pipelineContext = [:]
node {

    def registryProjet = 'registry.gitlab.com/xavki/presentations-jenkins'
    def IMAGE = "${registryProjet}:version-${env.BUILD_ID}"
    def CONTAINER_NAME = "run-${env.BUILD_ID}"
    def PORT = 9000

    echo "IMAGE = ${IMAGE}"

    stage('Clone') {
        checkout scm
    }

    def img = null

    stage('Build') {
        img = docker.build(IMAGE, '.')
    }

    stage('Run') {
        bat "docker stop run-13 || exit 0"
        bat "docker rm run-13 || exit 0"
        bat "docker run -d --name run-13 -p 9000:80 registry.gitlab.com/xavki/presentations-jenkins:version-13"
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push('latest')
            img.push()
        }
    }
}
