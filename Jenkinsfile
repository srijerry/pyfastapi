@Library('my-shared-library') _

pipeline{

    agent any

    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'choose create/destroy')
        string(name: 'appname', description: "name of the application", defaultValue: 'myapp')
        string(name: 'buildno', description: "tag of the application", defaultValue: 'v1')
        string(name: 'hubuser', description: "name of the dockeruser", defaultValue: 'sri0123')
        string(name: 'awsaccount', description: "AWS account ID", defaultValue: '982210731409')
        string(name: 'region', description: "AWS account ID", defaultValue: 'us-east-2')
        
    }

    stages{

        stage('Git checkout'){

        when { expression  {  params.action == 'create' } }

            steps{

                script{
                gitCheckout(
                    branch: "main",
                    url: "https://github.com/srijerry/java-app.git"
                )
                }
            }
        }
        stage('unit test maven'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    mvntest()

                }
            }
        }
        stage('intergartion test maven'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    mavenintegrationtest()

                }
            }
        }
        stage('static code analysis'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{
                    
                    def SonarQubecredentialsId = 'sonar-api'
                    staticcodeanalysis(SonarQubecredentialsId)

                }
            }
        }
        stage('QualityCheckStatus : soanr'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{
                    
                    def SonarQubecredentialsId = 'sonar-api'
                    QualityCheckStatus(SonarQubecredentialsId)

                }
            }
        }
        stage('maven Build'){

        when { expression  { params.action == 'create' } }
            
            steps{

                script{

                    mvnBuild()

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

                    ECRpush("${JOB_NAME}","${BUILD_NUMBER}","${params.awsaccount}","${params.region}")

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