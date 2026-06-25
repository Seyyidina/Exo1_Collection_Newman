pipeline {
    agent any

    parameters {
        choice(
            name: 'Environnement',
            choices: ['Env1', 'Env2'],
            description: 'Choisir un environnement'
        )
    }

    stages {
        stage('Global Stage') {

            agent {
                docker {
                    image 'node:lts-alpine3.24'
                    args '-u root --entrypoint='
                }
            }

            stages {

                stage('Install Dependencies') {
                    steps {
                        sh '''
                            npm install --save-dev newman
                            npm install --save-dev newman-reporter-allure
                        '''
                    }
                }

                stage('Clean Allure Results') {
                    steps {
                        sh '''
                            echo "Suppression du cache Allure..."
                            rm -rf output
                            mkdir -p output/allure-results
                            echo "Dossier output nettoyé avec succès"
                        '''
                    }
                }

                stage('Run User Test') {
                    steps {

                        sh """
                            npx newman run Collection.json \
                              -e ./Environment/${params.Environnement}.json \
                              --reporters cli,allure \
                              --reporter-allure-resultsDir output/allure-results
                        """

                        sh '''
                            echo "Contenu généré :"
                            find output || true
                        '''

                        stash(
                            name: 'output',
                            includes: 'output/**/*',
                            allowEmpty: true
                        )
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                unstash 'output'
            }

            archiveArtifacts(
                artifacts: 'output/**/*',
                allowEmptyArchive: true
            )

            allure(
                includeProperties: false,
                jdk: '',
                results: [[path: 'output/allure-results']]
            )
        }
    }
}