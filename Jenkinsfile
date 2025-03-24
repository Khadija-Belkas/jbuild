def pipelineContext = [:]
node {
    // Configuration
    def registryProjet = 'registry.gitlab.com/xavki/presentations-jenkins'
    def IMAGE = "${registryProjet}:version-${env.BUILD_ID}"
    def CONTAINER_NAME = "run-${env.BUILD_ID}"
    def PORT = 9050  // 👈 Port changé ici

    stage('Clone') {
        checkout scm
    }

    stage('Build') {
        docker.build(IMAGE, '.')
    }

    stage('Run') {
        // Nettoyage des conteneurs existants sur le port 9050
        bat '''
            FOR /F "tokens=*" %%i IN ('docker ps -q --filter "publish=9050"') DO (
                docker stop %%i
                docker rm %%i
            )
        '''
        // Lancement avec le bon port
        bat "docker run -d --name ${CONTAINER_NAME} -p ${PORT}:80 ${IMAGE}"
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            docker.image(IMAGE).push()
        }
    }
}
