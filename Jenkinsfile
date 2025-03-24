pipeline {
    agent any
    
    environment {
        // Configuration Docker - TOUTES LES VARIABLES ICI
        DOCKER_REGISTRY = 'registry.gitlab.com'
        IMAGE_NAME = 'xavki/presentations-jenkins'
        HOST_PORT = '9050'  // PORT DÉFINITIF FIXÉ ICI
        CONTAINER_NAME = "run-${env.BUILD_ID}"
    }

    stages {
        stage('Nettoyage Complet') {
            steps {
                script {
                    // 1. Force l'arrêt de tous les conteneurs utilisant le port
                    bat """
                        echo "=== NETTOYAGE DES CONTENEURS EXISTANTS ==="
                        FOR /F "tokens=*" %%i IN ('docker ps -aq --filter "publish=${HOST_PORT}"') DO (
                            echo "Arrêt du conteneur %%i"
                            docker stop %%i
                            docker rm %%i
                        )
                        
                        // 2. Supprime le conteneur actuel s'il existe
                        docker stop ${CONTAINER_NAME} || echo "Aucun conteneur à arrêter"
                        docker rm ${CONTAINER_NAME} || echo "Aucun conteneur à supprimer"
                    """
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Build avec tag unique
                    docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${env.BUILD_ID}", '.')
                }
            }
        }

        stage('Run') {
            steps {
                script {
                    // Lancement avec vérification
                    bat """
                        echo "=== LANCEMENT DU CONTENEUR SUR LE PORT ${HOST_PORT} ==="
                        docker run -d \\
                            --name ${CONTAINER_NAME} \\
                            -p ${HOST_PORT}:80 \\
                            ${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${env.BUILD_ID}
                        
                        echo "=== VÉRIFICATION ==="
                        timeout(time: 5, unit: 'SECONDS') {
                            bat "curl http://localhost:${HOST_PORT} || echo \"Le conteneur ne répond pas encore\""
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
