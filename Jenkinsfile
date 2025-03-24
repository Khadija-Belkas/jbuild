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
        // Stop & Remove the container if it exists (to avoid port conflict)
        bat "docker stop ${CONTAINER_NAME} || exit 0"
        bat "docker rm ${CONTAINER_NAME} || exit 0"

        // Run the container using the freshly built image
        bat "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE}"
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push('latest')
            img.push()
        }
    }
}
