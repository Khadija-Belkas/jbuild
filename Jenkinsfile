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
        // 1. Supprimer tous les conteneurs qui utilisent déjà le port 9000
        bat '''
        for /f "tokens=1" %%i in ('docker ps -q --filter "publish=9000"') do (
            docker stop %%i
            docker rm %%i
        )
        '''

        // 2. Lancer le nouveau conteneur proprement avec un nom unique
        bat "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE}"
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push('latest')
            img.push()
        }
    }
}
