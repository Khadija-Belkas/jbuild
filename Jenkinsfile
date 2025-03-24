def pipelineContext = [:]
node {

    def registryProjet = 'registry.gitlab.com/xavki/presentations-jenkins'
    def IMAGE = "${registryProjet}:version-${env.BUILD_ID}"
    def CONTAINER_NAME = "run-${env.BUILD_ID}"
    def PORT = 9060 // ✅ Nouveau port ici

    echo "✅ Jenkinsfile mis à jour - PORT utilisé = ${PORT}"
    echo "✅ IMAGE = ${IMAGE}"
    echo "✅ CONTAINER = ${CONTAINER_NAME}"

    stage('Clone') {
        checkout scm
    }

    def img = null

    stage('Build') {
        img = docker.build(IMAGE, '.')
    }
    stage('Run') {
    steps {
        // Arrêter et supprimer un ancien conteneur s’il existe
        bat """
        docker stop run-${BUILD_ID} || exit 0
        docker rm run-${BUILD_ID} || exit 0
        docker run -d --name run-${BUILD_ID} -p 9061:80 ${IMAGE}
        """

        // Petit délai pour que le conteneur démarre correctement
        sleep time: 2, unit: 'SECONDS'

        // Tester que l’application répond bien
        bat 'curl http://localhost:9061'
        }
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push('latest')
            img.push()
        }
    }

    stage('Done') {
        echo "✅ Application disponible sur : http://localhost:${PORT}"
    }
}
