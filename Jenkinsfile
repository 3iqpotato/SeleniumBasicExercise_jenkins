pipeline {
    agent any

    stages {
        stage('Setup ChromeDriver') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                            sudo apt-get update -y
                            sudo apt-get install -y wget unzip google-chrome-stable
                            CHROME_DRIVER_VERSION=$(curl -s https://chromedriver.storage.googleapis.com/LATEST_RELEASE)
                            wget -N https://chromedriver.storage.googleapis.com/${CHROME_DRIVER_VERSION}/chromedriver_linux64.zip
                            unzip chromedriver_linux64.zip -d /usr/local/bin/
                            chmod +x /usr/local/bin/chromedriver
                        '''
                    } else {
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