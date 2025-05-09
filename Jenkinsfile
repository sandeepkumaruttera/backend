def appVersion = '' // Declared at top as a Groovy variable

pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    triggers {
        githubPush() // Enables GitHub webhook trigger
    }
    parameters {
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment {
        nexusUrl = '54.196.104.29:8081'
        region = "us-east-1"
        account_id = "650732254329"
    }
    stages {
        stage('Read the Version') {
            steps {
                script {
                    def packageJson = readJSON file: 'package.json'
                    appVersion = packageJson.version
                    echo "Application version: ${appVersion}"
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                sh """
                    npm install
                    ls -ltr
                    echo "Application version: ${appVersion}"
                """
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build') {
            steps {
                sh """
                    zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                    ls -ltr
                """
            }
        }

        stage('Nexus Artifact Upload') {
            steps {
                script {
                    nexusArtifactUploader(
                        nexusVersion: 'nexus3',
                        protocol: 'http',
                        nexusUrl: "${nexusUrl}",
                        groupId: 'com.expense',
                        version: "${appVersion}",
                        repository: "backend",
                        credentialsId: 'nexus-auth',
                        artifacts: [
                            [
                                artifactId: "backend",
                                classifier: '',
                                file: "backend-${appVersion}.zip",
                                type: 'zip'
                            ]
                        ]
                    )
                }
            }
        }

        stage('Deploy') {
            when {
                expression {
                    return params.deploy
                }
            }
            steps {
                sh 'echo Deploying backend version ${appVersion}...'
            }
        }
    }

    post {
        always {
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
