@Library('my-shared-library') _

pipeline{

    agent any

    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'choose create/destroy')
        string(name: 'appname', description: "name of the application", defaultValue: 'pyapp')
        string(name: 'buildno', description: "tag of the application", defaultValue: 'v1')
        string(name: 'hubuser', description: "name of the dockeruser", defaultValue: 'sri0123')
        string(name: 'awsaccount', description: "AWS account ID", defaultValue: '982210731409')
        string(name: 'region', description: "AWS account ID", defaultValue: 'us-east-2')
        
    }
    environment{

        ACCESS_KEY = credentials('AWS_ACCESS_KEY_ID')
        SECRET_KEY = credentials('AWS_SECRET_KEY_ID')
    }

    stages{

        stage('Git checkout'){

        when { expression  {  params.action == 'create' } }

            steps{

                script{
                gitCheckout(
                    branch: "main",
                    url: "https://github.com/srijerry/pyfastapi.git"
                )
                }
            }
        }
        stage('static code analysis'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{
                    
                    def scannerHome = tool 'sonar-api1';
                    withSonarQubeEnv() {
                    sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }                    
        stage('QualityCheckStatus : sonar'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{
                    
                    def SonarQubecredentialsId = 'sonar-api'
                    QualityCheckStatus(SonarQubecredentialsId)

                }
            }
        }
        stage('Docker build'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    dockerBuild("${params.appname}","${BUILD_NUMBER}","${params.hubuser}")

                }
            }
        }
        stage('trivy scan '){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    trivyscan("${params.appname}","${BUILD_NUMBER}","${params.hubuser}")

                }
            }
        }
        stage('docker push :DockerHub'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    dockerpush("${params.appname}","${BUILD_NUMBER}","${params.hubuser}")

                }
            }
        }  
        stage('ecr push'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    ECRpush("${JOB_NAME}","${BUILD_NUMBER}","${params.awsaccount}","${params.region}",'$ACCESS_KEY','$SECRET_KEY')

                }
            }
        }
        stage('docker image cleanup'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    dockerimgcleanup("${params.appname}","${BUILD_NUMBER}","${params.hubuser}")

                }
            }
        }      
    }
}            