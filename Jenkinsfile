pipeline {

    agent any
    stages{

        stage('Build'){

            steps{

                echo 'Building app'
             
                git credentialsId: 'git_credentials', url: 'https://github.com/kjop118/deltachat-desktop'
                dir('Docker'){
                    
                    sh '''
                        curl -L "https://github.com/docker/compose/releases/download/1.29.1/docker-compose-$(uname -s)-$(uname -m)" -o ~/docker-compose
                        chmod +x ~/docker-compose
                        ~/docker-compose up -d chat-build
                    ''' 

                }
                
                
                echo "${currentBuild.currentResult}"
                sh "exit 1"
            }
            
            post {
                    success {
                        emailext attachLog: true, 
                            body: """Build status: ${currentBuild.currentResult},
Job: ${env.JOB_NAME},
More informations in attachment""", 
                            recipientProviders: [developers()], 
                            subject: 'Build passed', 
                            to: 'jkarolina1@interia.pl'
                    }

                    failure {
                        emailext attachLog: true, 
                            body: """Build status: ${currentBuild.currentResult},
Job: ${env.JOB_NAME},
More informations in attachment""", 
                            recipientProviders: [developers()], 
                            subject: 'Build failed', 
                            to: 'jkarolina1@interia.pl'
                    }
                }
        }
        
        stage('Test') {
            
            steps{
                

                echo 'Start testing'
                dir('Docker'){
                    sh '~/docker-compose up -d chat-test'
                }
            }
            
            post {

                success {
                    emailext attachLog: true, 
                        body: """Test status: ${currentBuild.currentResult},
Job ${env.JOB_NAME}, 
More informations in attachment""", 
                        recipientProviders: [developers()], 
                        subject: 'Test passed', 
                        to: 'jkarolina1@interia.pl'
                }

                failure {
                    emailext attachLog: true, 
                        body: """Test status: ${currentBuild.currentResult},
Job ${env.JOB_NAME}, 
More informations in attachment""", 
                        recipientProviders: [developers()], 
                        subject: 'Test failed', 
                        to: 'jkarolina1@interia.pl'
                }
            }
        }
    }

    
    post {

        success {
            emailext attachLog: true, 
                body: """Pipeline status: ${currentBuild.currentResult},
Job ${env.JOB_NAME}, 
More informations in attachment""", 
                recipientProviders: [developers()], 
                subject: 'Pipeline passed', 
                to: 'jkarolina1@interia.pl'
        }

        failure {
            emailext attachLog: true, 
                body: """Pipeline status: ${currentBuild.currentResult},
Job ${env.JOB_NAME},
More informations in attachment""", 
                recipientProviders: [developers()], 
                subject: 'Pipeline failed', 
                to: 'jkarolina1@interia.pl'
        }
    }
}
