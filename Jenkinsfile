// Jenkinsfile – Pipeline CI : Git → Jenkins → Checkout → Maven Compile → SUCCESS
// Syntaxe déclarative : pipeline { agent → tools → stages { stage { steps } } → post }

pipeline {

    // 2 - agent : exécuter le pipeline sur n'importe quel agent disponible
    agent any

    // 3 - tools : outils configurés au préalable dans
    //     Administrer Jenkins → Tools (les noms doivent être IDENTIQUES)
    tools {
        jdk   'JDK17'
        maven 'M2_HOME'
    }

    // 6 - stages : les étapes du pipeline
    stages {

        stage('Checkout') {
            steps {
                echo 'Récupération du code depuis GitHub...'
                git branch: 'main',
                    url: 'https://github.com/farah858/gestion-projets-backend.git'
            }
        }

        stage('Compile') {
            steps {
                echo 'Compilation du projet avec Maven...'
                bat 'mvn compile'     // Jenkins sous Windows : bat (sous Linux : sh)
            }
        }
    }

    // 7 - post : actions exécutées à la fin selon le résultat
    post {
        success {
            echo 'BUILD SUCCESS : compilation terminée avec succès.'
        }
        failure {
            echo 'BUILD FAILURE : la compilation a échoué.'
        }
        always {
            echo 'Fin du pipeline.'
        }
    }
}
