pipeline {
    agent any

    environment {
        // (Opcional) Define aquí las credenciales para tu registro de Docker
        // DOCKER_CREDENTIALS = credentials('tu-docker-hub-credentials-id')
    }

    stages {
        // ===========================================
        // ETAPA 1: CONSTRUCCIÓN Y PRUEBA DEL BACKEND
        // ===========================================
        stage('Build & Test Backend') {
            steps {
                script {
                    dir('backend/MercappBackend') {
                        echo '✅ Iniciando construcción del Backend...'
                        // Utiliza el wrapper de Maven para construir el proyecto y ejecutar pruebas
                        sh './mvnw clean package'
                        echo 'Backend construido y probado exitosamente.'
                    }
                }
            }
        }

        // ===========================================
        // ETAPA 2: CONSTRUCCIÓN Y PRUEBA DEL FRONTEND
        // ===========================================
        stage('Build & Test Frontend') {
            steps {
                script {
                    dir('frontend/mercappfrontend') {
                        echo '✅ Iniciando construcción del Frontend...'
                        // Instala dependencias y ejecuta pruebas
                        sh 'npm install'
                        sh 'npm test'
                        sh 'npm run build'
                        echo 'Frontend construido y probado exitosamente.'
                    }
                }
            }
        }

        // ===========================================
        // ETAPA 3: CONSTRUCCIÓN DE IMÁGENES DOCKER
        // ===========================================
        stage('Build Docker Images') {
            steps {
                script {
                    echo '🐳 Construyendo imágenes de Docker...'
                    // Utiliza el docker-compose.yml de la raíz para construir las imágenes
                    sh 'docker-compose build'
                    echo 'Imágenes de Docker construidas exitosamente.'
                }
            }
        }

        // ==========================================================
        // ETAPA 4: (Opcional) PUBLICACIÓN DE IMÁGENES EN DOCKER HUB
        // ==========================================================
        /*
        stage('Push Docker Images') {
            steps {
                script {
                    // Inicia sesión en Docker Hub y publica las imágenes
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                        sh 'docker-compose push'
                        echo 'Imágenes publicadas en Docker Hub.'
                    }
                }
            }
        }
        */

        // ===========================================
        // ETAPA 5: DESPLIEGUE DE LA APLICACIÓN
        // ===========================================
        stage('Deploy Application') {
            steps {
                script {
                    echo '🚀 Desplegando la aplicación con Docker Compose...'
                    sh 'docker-compose down' // Detiene contenedores anteriores si existen
                    sh 'docker-compose up -d' // Inicia los nuevos contenedores en segundo plano
                    echo '🎉 Aplicación desplegada exitosamente.'
                }
            }
        }
    }

    post {
        always {
            // Limpia los contenedores después de la ejecución
            echo '🧹 Limpiando...'
            sh 'docker-compose down --volumes --remove-orphans'
        }
    }
}