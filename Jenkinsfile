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


        stage('Deploy') {
            
            steps{
                echo 'Deploying'

                dir('Docker'){
                    sh 'docker tag chat:latest kjop118/chat:latest'
                    sh 'docker images'
                    //sh 'docker save -o /var/jenkins_home/workspace/my-chat-pipeline/Docker/chatBuild.tar kjop118/chat:latest'
                    //sh 'docker build -t ubuntu-deploy -f Dockerfile_ubuntu .'
                    sh 'docker run -d ubuntu-deploy'
                    sh 'rsync -avz chatBuild.tar ubuntu-deploy:/tmp/'
                    //sh 'docker cp /var/jenkins_home/workspace/my-chat-pipeline/Docker/chatBuild.tar ubuntu-deploy:/tmp/'
                    sh 'docker load -i /var/jenkins_home/workspace/my-chat-pipeline/Docker/chatBuild.tar'
                    sh 'ls'
                }

                
            }
            
            post {

                success {
                    emailext attachLog: true, 
                        body: """Test status: ${currentBuild.currentResult},
Job ${env.JOB_NAME}, 
More informations in attachment""", 
                        recipientProviders: [developers()], 
                        subject: 'Deploy passed', 
                        to: 'jkarolina1@interia.pl'
                }

                failure {
                    emailext attachLog: true, 
                        body: """Test status: ${currentBuild.currentResult},
Job ${env.JOB_NAME}, 
More informations in attachment""", 
                        recipientProviders: [developers()], 
                        subject: 'Deploy failed', 
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
