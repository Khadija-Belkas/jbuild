pipeline {
    agent any
    
    environment {
        // Configuration Docker
        DOCKER_REGISTRY = 'registry.gitlab.com'
        IMAGE_NAME = 'xavki/presentations-jenkins'
        HOST_PORT = '9050'  // Port définitif
        CONTAINER_NAME = "run-${env.BUILD_ID}"
    }

    stages {
        stage('Clean') {
            steps {
                script {
                    // Nettoyage radical en 3 étapes
                    bat """
                        echo "=== NETTOYAGE DES ANCIENS CONTENEURS ==="
                        // 1. Arrêt des conteneurs en conflit
                        FOR /F "tokens=*" %%i IN ('docker ps -aq --filter "publish=${HOST_PORT}"') DO (
                            docker stop %%i
                            docker rm %%i
                        )
                        
                        // 2. Suppression du conteneur actuel s'il existe
                        docker stop ${CONTAINER_NAME} || echo "Aucun conteneur à arrêter"
                        docker rm ${CONTAINER_NAME} || echo "Aucun conteneur à supprimer"
                        
                        // 3. Vérification
                        echo "Conteneurs restants:"
                        docker ps -a
                    """
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${env.BUILD_ID}", '.')
                }
            }
        }

        stage('Run') {
            steps {
                script {
                    bat """
                        echo "=== LANCEMENT DU CONTENEUR ==="
                        echo "Port utilisé: ${HOST_PORT}"
                        docker run -d \\
                            --name ${CONTAINER_NAME} \\
                            -p ${HOST_PORT}:80 \\
                            ${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${env.BUILD_ID}
                        
                        echo "=== VERIFICATION ==="
                        timeout(time: 10, unit: 'SECONDS') {
                            bat "curl http://localhost:${HOST_PORT}"
                        }
                    """
                }
            }
        }
    }

    post {
        always {
            echo "=== NETTOYAGE FINAL ==="
            bat """
                docker stop ${CONTAINER_NAME} || echo "Échec arrêt conteneur"
                docker rm ${CONTAINER_NAME} || echo "Échec suppression conteneur"
            """
        }
    }
}
