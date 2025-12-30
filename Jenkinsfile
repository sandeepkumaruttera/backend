pipeline {
    agent any
    // agent {
    //     label 'AGENT-1'
    // }
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
        account_id = "696919879899"
    }
    stages {
        stage('read the version'){
            steps{
                script{
                    def packageJson = readJSON file: 'package.json'
                    appVersion = "${packageJson.version}-${env.BUILD_NUMBER}"
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
        //             echo "Chintu@123" | docker login --username joindevops006 --password-stdin

        //              docker build -t  joindevops006/joindevops:${appVersion} .

        //              docker push  joindevops006/joindevops:${appVersion}
        //          """
        //     }
        // } 

    //     stage('Deploy'){
    //         steps{
    //             sh """
    //                 aws eks update-kubeconfig --region us-east-1 --name abn
    //                 cd helm
    //                 sed -i 's/IMAGE_VERSION/${appVersion}/g' values.yaml
    //                 helm install backend .
    //             """
    //         }
    //    }  
        stage('Deploy') {
            steps {
                sh 'echo this is deploy'
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