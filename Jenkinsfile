pipeline {

    agent any

    tools {
        jdk   'JDK17'
        maven 'M2_HOME'
    }

    environment {
        IMAGE_LOCAL  = 'backend-app:latest'
        IMAGE_REMOTE = 'farrrraaaahhh/backend-app:latest'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/farah858/gestion-projets-backend.git'
            }
        }

        stage('Build & Test Maven') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t %IMAGE_LOCAL% .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    bat 'echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin'
                    bat 'docker tag %IMAGE_LOCAL% %IMAGE_REMOTE%'
                    bat 'docker push %IMAGE_REMOTE%'
                }
            }
        }

        stage('Deploiement MySQL') {
            steps {
                bat 'docker network create devops-net || echo reseau deja cree'
                bat 'docker rm -f mysql || echo pas de conteneur mysql'
                bat 'docker run -d --name mysql --network devops-net -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=test_db mysql:8.0'
                // attendre que MySQL soit pret
                retry(20) {
                    sleep 5
                    bat 'docker exec mysql mysqladmin ping -h 127.0.0.1 -uroot -proot --silent'
                }
            }
        }

        stage('Deploiement backend-app') {
            steps {
                bat 'docker rm -f backend-app || echo pas de conteneur backend-app'
                bat 'docker pull %IMAGE_REMOTE%'
                bat 'docker run -d --name backend-app --network devops-net -p 8089:8080 -e SPRING_DATASOURCE_URL=jdbc:mysql://mysql:3306/test_db?allowPublicKeyRetrieval=true -e SPRING_DATASOURCE_USERNAME=root -e SPRING_DATASOURCE_PASSWORD=root %IMAGE_REMOTE%'
            }
        }

        stage('Verification') {
            steps {
                bat 'docker ps'
                retry(12) {
                    sleep 5
                    bat 'docker logs backend-app 2>&1 | findstr /C:"Started BackendApplication"'
                }
                bat 'docker logs backend-app'
            }
        }
    }

    post {
        success {
            echo 'Pipeline CD termine : backend-app et mysql sont deployes.'
        }
        failure {
            echo 'Le pipeline a echoue, voir la Console Output.'
        }
        always {
            bat 'docker logout || echo logout'
        }
    }
}
