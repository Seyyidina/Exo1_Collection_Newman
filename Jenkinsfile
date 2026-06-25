pipeline{
    agent any
    parameters{
        choice(name: 'Environnement', choices: ['Env1', 'Env2'], description: 'Choisir un Env.')
    }
    stages{
        stage('global stage'){
            agent{
                docker{
                    image 'node:lts-alpine3.24'
                    args '-u root --entrypoint='
                }
            }
            stages{
                stage('install deps'){
                    steps{
                        sh 'npm install --save-dev newman-reporter-allure'
                    }
                }

                stage('clean allure results'){
                    
                    steps{
                        sh '''
                            echo "Suppression du cache Allure..."
                            rm -rf output
                            mkdir -p output
                            echo "Dossier output nettoyé avec succès"
                        '''
                    }
                }
        
                stage('run user test'){
                    steps{  
                        sh 'npx newman run Collection.json -e ./Environment/' + params.Environnement + '.json  --reporters cli,allure --reporter-allure-resultsDir output/allure-results'
                        stash name: 'output', includes: 'output/*'
                        }
                    }
                }
            }
        }
    }
    post{
        always{
            script{
                    unstash 'output'
                    archiveArtifacts '/*'
                    allure includeProperties: false,
                           jdk: '',
                           results: [[path: 'output/']]
            }
        }
    }

