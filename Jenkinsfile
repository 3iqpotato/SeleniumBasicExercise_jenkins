pipeline {
    agent any

    stages {
        stage('Setup ChromeDriver') {
            steps {
                script {

                        bat '''
                            choco install googlechrome -y
                            $chromeDriverVersion = (Invoke-WebRequest -Uri "https://chromedriver.storage.googleapis.com/LATEST_RELEASE").Content
                            Invoke-WebRequest -Uri "https://chromedriver.storage.googleapis.com/$chromeDriverVersion/chromedriver_win32.zip" -OutFile "chromedriver.zip"
                            Expand-Archive -Path "chromedriver.zip" -DestinationPath "$env:ProgramFiles\\ChromeDriver" -Force
                            $env:PATH += ";$env:ProgramFiles\\ChromeDriver"
                        '''
                
                }
            }
        }

        stage('Run Tests') {
            steps {
                checkout scm
                bat 'dotnet test --configuration Release'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}