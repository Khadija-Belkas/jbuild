pipeline {
    agent any

    environment {
        // Configuration Docker
        REGISTRY_URL = 'registry.gitlab.com'
        PROJECT_PATH = 'xavki/presentations-jenkins'
        IMAGE_NAME = "${REGISTRY_URL}/${PROJECT_PATH}:version-${BUILD_ID}"
        CONTAINER_NAME = "run-${BUILD_ID}"
        HOST_PORT = "9050"  // Port définitivement changé à 9050
        CONTAINER_PORT = "80"
    }

    stages {
        stage('Vérification Port') {
            steps {
                script {
                    echo "=== CONFIGURATION ==="
                    echo "Image: ${IMAGE_NAME}"
                    echo "Port Mapping: ${HOST_PORT}:${CONTAINER_PORT}"
                    
                    // Vérification des conteneurs existants
                    bat '''
                        echo "Conteneurs utilisant le port 9050:"
                        docker ps --filter "publish=9050"
                    '''
                }
            }
        }

        stage('Nettoyage') {
            steps {
                script {
                    // Nettoyage radical en 3 étapes
                    bat '''
                        echo "1. Arrêt des conteneurs en conflit..."
                        FOR /F "tokens=*" %%i IN ('docker ps -q --filter "publish=9050"') DO (
                            docker stop %%i
                            docker rm %%i
                        )

                        echo "2. Suppression de l'ancien conteneur s'il existe..."
                        docker stop ${CONTAINER_NAME} || echo "Aucun conteneur à arrêter"
                        docker rm ${CONTAINER_NAME} || echo "Aucun conteneur à supprimer"

                        echo "3. Vérification finale..."
                        docker ps -a --filter "name=${CONTAINER_NAME}"
                    '''
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.build(IMAGE_NAME, '.')
                }
            }
        }

        stage('Run') {
            steps {
                script {
                    bat """
                        echo "Lancement du conteneur sur le port ${HOST_PORT}..."
                        docker run -d \\
                            --name ${CONTAINER_NAME} \\
                            -p ${HOST_PORT}:${CONTAINER_PORT} \\
                            --restart unless-stopped \\
                            ${IMAGE_NAME}

                        echo "Vérification:"
                        docker ps --format "table {{.Names}}\\t{{.Ports}}" | findstr "${CONTAINER_NAME}"
                    """
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry("https://${REGISTRY_URL}", 'reg1') {
                        docker.image(IMAGE_NAME).push()
                        docker.image(IMAGE_NAME).push('latest')
                    }
                }
            }
        }
    }

    post {
        always {
            echo "=== [Nettoyage Final] ==="
            bat """
                docker rm -f ${CONTAINER_NAME} || echo "Aucun conteneur à supprimer"
                docker rmi ${IMAGE_NAME} || echo "Aucune image à supprimer"
            """
        }
    }
}
