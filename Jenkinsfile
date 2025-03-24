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
        // 🔥 Arrêter et supprimer tout conteneur qui utilise déjà le port 9060
        bat """
        FOR /F "tokens=*" %%i IN ('docker ps -q --filter "publish=${PORT}"') DO (
            docker stop %%i
            docker rm %%i
        )
        """

        // 🚀 Lancer le nouveau conteneur avec la bonne image et le bon port
        bat "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE}"
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
