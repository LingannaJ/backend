pipeline {
    agent {
        label 'AGENT-1'
    }

     options {
            
            timeout(time: 30, unit: 'MINUTES')
            disableConcurrentBuilds()
            ansiColor('xterm')
    }
    environment{
        def appVersion = '' // variable declaration
        nexusUrl = 'nexus.shivdev.online:8081'
    }
    stages {
        stage('read the version'){
            steps{
                script{
                    def packageJson = readJSON.file 'package.json'
                    appVersion = packageJson.version 
                    echo "application version : $appVersion"
                }
            }
        }
        stage('Install Dependencies'){ 
            steps {
                sh """
                 npm install
                 ls -ltr
                 echo "application version : $appVersion"
                """
            }
        }
        stage('Build'){
            steps{
                sh"""
                zip -q -r backend-${AppVersion}.zip * -x Jenkinsfile -x backend-${AppVersion}.zip
                """
            }
        }
            
        stage('Nexus Artifact Upload'){
            steps{
                script{
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${AppVersion}",
                        repository: "backend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [artifactId: "backend",
                            classifier: '',
                            file: 'backend-' + "${AppVersion}" + '.zip',
                            type: 'zip']
                        ]
                    )
                }
            }
        }
                       
    }
    post { 
        
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'I will run when pipeline is success'
        }
        failure { 
            echo 'I will run when pipeline is failure'
        }
    }
}


