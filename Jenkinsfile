pipeline {
    agent any
    
    environment {
        // Configuration Docker
        DOCKER_REGISTRY = 'registry.gitlab.com'
        IMAGE_NAME = 'xavki/presentations-jenkins'
        HOST_PORT = '9050'  // Port fixé ici
    }

    stages {
        stage('Nettoyage Force') {
            steps {
                bat '''
                    echo "=== NETTOYAGE ==="
                    docker stop run-${BUILD_ID} || echo "Aucun conteneur à arrêter"
                    docker rm run-${BUILD_ID} || echo "Aucun conteneur à supprimer"
                    
                    // Supprime TOUS les conteneurs utilisant le port
                    FOR /F "tokens=*" %%i IN ('docker ps -aq --filter "publish=9050"') DO (
                        docker stop %%i
                        docker rm %%i
                    )
                '''
            }
        }

        stage('Build et Run') {
            steps {
                script {
                    // Build avec tag unique
                    docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${BUILD_ID}", '.')

                    // Run avec vérification
                    bat """
                        echo "=== LANCEMENT ==="
                        echo "Port utilisé: ${HOST_PORT}"
                        docker run -d \
                            --name run-${BUILD_ID} \
                            -p ${HOST_PORT}:80 \
                            ${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${BUILD_ID}
                        
                        echo "=== VERIFICATION ==="
                        docker ps --format "table {{.Names}}\t{{.Ports}}" | findstr "run-"
                    """
                }
            }
        }
    }
}pipeline {
    agent any
    
    environment {
        // Configuration Docker
        DOCKER_REGISTRY = 'registry.gitlab.com'
        IMAGE_NAME = 'xavki/presentations-jenkins'
        HOST_PORT = '9050'  // Port fixé ici
    }

    stages {
        stage('Nettoyage Force') {
            steps {
                bat '''
                    echo "=== NETTOYAGE ==="
                    docker stop run-${BUILD_ID} || echo "Aucun conteneur à arrêter"
                    docker rm run-${BUILD_ID} || echo "Aucun conteneur à supprimer"
                    
                    // Supprime TOUS les conteneurs utilisant le port
                    FOR /F "tokens=*" %%i IN ('docker ps -aq --filter "publish=9050"') DO (
                        docker stop %%i
                        docker rm %%i
                    )
                '''
            }
        }

        stage('Build et Run') {
            steps {
                script {
                    // Build avec tag unique
                    docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${BUILD_ID}", '.')

                    // Run avec vérification
                    bat """
                        echo "=== LANCEMENT ==="
                        echo "Port utilisé: ${HOST_PORT}"
                        docker run -d \
                            --name run-${BUILD_ID} \
                            -p ${HOST_PORT}:80 \
                            ${DOCKER_REGISTRY}/${IMAGE_NAME}:version-${BUILD_ID}
                        
                        echo "=== VERIFICATION ==="
                        docker ps --format "table {{.Names}}\t{{.Ports}}" | findstr "run-"
                    """
                }
            }
        }
    }
}
