def pipelineContext = [:]
node {

    def registryProjet = 'registry.gitlab.com/xavki/presentations-jenkins'
    def IMAGE = "${registryProjet}:version-${env.BUILD_ID}"
    def CONTAINER_NAME = "run-${env.BUILD_ID}"
    def PORT = 9061 // Port mis à jour pour cohérence avec docker run

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
        // Arrêter et supprimer un ancien conteneur s’il existe
        bat """
        docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE}
        """

        // Laisser le conteneur démarrer
        sleep time: 2, unit: 'SECONDS'

        // Tester que ça fonctionne
        bat "curl http://localhost:${PORT}"
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
