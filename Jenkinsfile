pipeline {
    agent any

    environment {
        // Configuration Docker
        DOCKER_REGISTRY = 'registry.gitlab.com'
        DOCKER_IMAGE = 'xavki/presentations-jenkins'
        HOST_PORT = '9050'  // Port fixé à 9050
        CONTAINER_PORT = '80'
    }

    stages {
        stage('Vérification Port') {
            steps {
                script {
                    echo "=== CONFIGURATION ==="
                    echo "Port Mapping: ${HOST_PORT}:${CONTAINER_PORT}"
                    
                    // Nettoyage radical
                    bat '''
                        echo "1. Arrêt des conteneurs en conflit..."
                        FOR /F "tokens=*" %%i IN ('docker ps -q --filter "publish=9050"') DO (
                            docker stop %%i
                            docker rm %%i
                        )

                        echo "2. Suppression de l'ancien conteneur..."
                        docker stop run-${BUILD_ID} || echo "Aucun conteneur à arrêter"
                        docker rm run-${BUILD_ID} || echo "Aucun conteneur à supprimer"
                    '''
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE}:version-${BUILD_ID}", '.')
                }
            }
        }

        stage('Run') {
            steps {
                script {
                    bat """
                        echo "Lancement sur le port ${HOST_PORT}"
                        docker run -d \\
                            --name run-${BUILD_ID} \\
                            -p ${HOST_PORT}:${CONTAINER_PORT} \\
                            ${DOCKER_REGISTRY}/${DOCKER_IMAGE}:version-${BUILD_ID}
                    """
                }
            }
        }
    }
}
