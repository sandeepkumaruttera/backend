pipeline {
    agent any
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters{
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment{
        def appVersion = '' //variable declaration
       // nexusUrl = '98.82.200.204:8081'
        region = "us-east-1"
        account_id = "650732254329"
    }
    stages {
        stage('read the version'){
            steps{
                script{
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "application version: $appVersion"
                }
            }
        }
        stage('Install Dependencies') {
            steps {
               sh """
                npm install
                ls -ltr
                echo "application version: $appVersion"
               """
            }
        }
        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build'){
            steps{
                sh """
                zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                ls -ltr
                """
            }
        }
        //  stage('Docker build'){
        //      steps{
        //          sh """
        //              docker login --username joindevops006 --password Chintu@123

        //              docker build -t  joindevops006/joindevops:${appVersion} .

        //              docker push  joindevops006/joindevops:${appVersion}
        //          """
        //      }
        //  } 

        // stage('Deploy'){
        //     steps{
        //         sh """
        //             aws eks update-kubeconfig --region us-east-1 --name abn
        //             cd helm
        //             sed -i 's/IMAGE_VERSION/${appVersion}/g' values.yaml
        //             helm install backend .
        //         """
        //     }
       // } 

        
        // stage('Sonar Scan'){
        //     environment {
        //         scannerHome = tool 'sonar-6.0' //referring scanner CLI
        //     }
        //     steps {
        //         script {
        //             withSonarQubeEnv('sonar-6.0') { //referring sonar server at manage-jenkins/systems 
        //                 sh "${scannerHome}/bin/sonar-scanner"
        //             }
        //         }
        //     }
        // } 

        // stage('Nexus Artifact Upload'){
        //     steps{
        //         script{
        //             nexusArtifactUploader(
        //                 nexusVersion: 'nexus3',
        //                 protocol: 'http',
        //                 nexusUrl: "${nexusUrl}",
        //                 groupId: 'com.expense',
        //                 version: "${appVersion}",
        //                 repository: "backend",
        //                 credentialsId: 'nexus-auth',
        //                 artifacts: [
        //                     [artifactId: "backend" ,
        //                     classifier: '',
        //                     file: "backend-" + "${appVersion}" + '.zip',
        //                     type: 'zip']
        //                 ]
        //             )
        //         }
        //     }
        // }  
        // stage('Deploy') {
        //     steps {
        //         sh 'echo this is deploy'
        //     }
        // }
        stage('Deploy to Local Jenkins Linux') {
            steps {
               script {
                    def deployPath = "/opt/backend-deploy"
                 sh """
                    mkdir -p ${deployPath}
                    unzip -o backend-${appVersion}.zip -d ${deployPath}
                    echo "Unzipped contents to ${deployPath}"
                    ls -ltr ${deployPath}
                   """
                }
            }
        }





        /* stage('Deploy'){
            when{
                expression{
                    params.deploy
                }
            }
            steps{
                script{
                    def params = [
                        string(name: 'appVersion', value: "${appVersion}")
                    ]
                    build job: 'backend-deploy', parameters: params, wait: false
                }
            }
        } */
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