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
        // 🧹 Étape 1 : Supprimer n'importe quel conteneur qui utilise le port 9000
        bat '''
        FOR /F "tokens=*" %%i IN ('docker ps -q --filter "publish=9000"') DO (
            docker stop %%i
            docker rm %%i
        )
        '''

        // 🚀 Étape 2 : Lancer le nouveau conteneur avec la bonne image et un nom unique
        bat "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE}"
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push('latest')
            img.push()
        }
    }
}
