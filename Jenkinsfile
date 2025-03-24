def pipelineContext = [:]
node {
    // Configuration
    def registryProjet = 'registry.gitlab.com/xavki/presentations-jenkins'
    def IMAGE = "${registryProjet}:version-${env.BUILD_ID}"
    def CONTAINER_NAME = "run-${env.BUILD_ID}"
    def PORT = 9050  // 👈 Changé de 9080 à 9050
    
    echo "Build ID: ${env.BUILD_ID}"
    echo "Image: ${IMAGE}"
    echo "Container: ${CONTAINER_NAME}"
    echo "Port: ${PORT}"

    stage('Clone') {
        checkout scm
    }

    def img = null

    stage('Build') {
        img = docker.build(IMAGE, '.')
    }

    stage('Run') {
        // Nettoyage des anciens conteneurs
        script {
            try {
                // Arrêt et suppression des conteneurs utilisant le même nom
                bat "docker stop ${CONTAINER_NAME} || true"
                bat "docker rm ${CONTAINER_NAME} || true"
                
                // Arrêt des conteneurs utilisant le même port (maintenant 9050)
                bat '''
                FOR /F "tokens=*" %%i IN ('docker ps -q --filter "publish=9050"') DO (
                    docker stop %%i
                    docker rm %%i
                )
                '''
            } catch (e) {
                echo "Cleanup warning: ${e.message}"
            }
        }

        // Lancement du nouveau conteneur avec le port 9050
        bat """
            docker run -d \
                --name ${CONTAINER_NAME} \
                -p ${PORT}:80 \
                --restart unless-stopped \
                ${IMAGE}
        """
        
        // Vérification
        bat "docker ps --filter name=${CONTAINER_NAME}"
    }

    stage('Push') {
        docker.withRegistry('https://registry.gitlab.com', 'reg1') {
            img.push()  // Pousse la version avec le BUILD_ID
            img.push('latest')  // Pousse aussi comme 'latest'
        }
    }

    // Nettoyage optionnel
    stage('Cleanup') {
        bat "docker rmi ${IMAGE} || true"
    }
}
