pipeline {
    agent any

    parameters {
        string(name: 'tomcat_staging', defaultValue: 'tomcat_staging', description: 'Staging server')
        string(name: 'tomcat_prod', defaultValue: 'tomcat_prod', description: 'Production server')
    }

    triggers{
        pollSCM('* * * * *')
    }

    tools {
        maven 'localMaven'
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "docker cp **/target/*.war ${params.tomcat_staging}:webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "docker cp **/target/*.war ${params.tomcat_prod}:webapps"
                    }
                }
            }
        }
    }
}