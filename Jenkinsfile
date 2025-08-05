pipeline {
    agent any

    stages {
        stage('Setup ChromeDriver') {
            steps {
                bat '''
                    choco install googlechrome -y
                    curl -L -o chromedriver.zip https://chromedriver.storage.googleapis.com/LATEST_RELEASE/chromedriver_win32.zip
                    mkdir "%ProgramFiles%\\ChromeDriver" 2>nul
                    powershell -Command "Expand-Archive -Path chromedriver.zip -DestinationPath \\"$env:ProgramFiles\\ChromeDriver\\" -Force"
                    setx PATH "%PATH%;%ProgramFiles%\\ChromeDriver"
                '''
            }
        }

        stage('Restore') {
            steps {
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build --no-restore --configuration Release'
            }
        }

        stage('Run Tests') {
            steps {
                checkout scm
                bat 'dotnet test --no-build --configuration Release --verbosity normal'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
