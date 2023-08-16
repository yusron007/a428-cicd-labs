pipeline {
    agent {
        docker {
            image 'node:16-buster-slim' 
            args '-p 3000:3000' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'npm install' 
            }
        }
        stage('Test') {
            steps {
                sh './jenkins/scripts/test.sh'
            }
        }
        stage('Manual Approval') {
            steps {
                script {
                    def userInput = input(
                        id: 'userInput', message: 'Lanjutkan ke tahap Deploy?',
                        parameters: [
                            [$class: 'ChoiceParameterDefinition', choices: 'Proceed\nAbort', description: 'Pilih tindakan', name: 'ACTION']
                        ]
                    )
                    
                    if (userInput == 'Abort') {
                        currentBuild.result = 'ABORTED'
                        error("Pipeline dihentikan oleh pengguna")
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Sudah selesai menggunakan React App? (Klik "Proceed" untuk mengakhiri)'
                sh './jenkins/scripts/kill.sh'
                echo 'Waiting for 1 minute...'
                sleep time: 60, unit: 'SECONDS' // Memberikan jeda selama 1 menit
            }
        }
    }
}