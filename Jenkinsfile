pipeline{
    agent any
    parameters{
        choice(name: 'Environnement', choices: ['Env1', 'Env2'], description: 'Choisir un Env.')
    }
    stages{
        stage('global stage'){
            agent{
                docker{
                    image 'lts-alpine3.24:latest'
                    args '-u root --entrypoint='
                }
            }
            stages{
                stage('install deps'){
                    steps{
                        sh 'npm ci'
                    }
                }

                stage('clean allure results'){
                    
                    steps{
                        sh 'npx newman run Collection.json -e ./Environment/' + params.Environnement + '.json  --reporters cli,allure --reporter-allure-resultsDir output/allure-results'
                    }
                }
        
                stage('run user test'){
                    steps{
                        sh '''
                            "
                            rm -rf output
                            mkdir -p output
                            echo "Dossier output nettoyé avec succès"
                        '''
                        
                    }
                }
            }
        }
    }
}
    }
    post{
        always{
            script{
                if(params.ALLURE){
                    unstash 'allure-results'
                    archiveArtifacts 'output/*'
                    allure includeProperties: false,
                           jdk: '',
                           results: [[path: 'output/']]
                }
            }
        }
    }
}
