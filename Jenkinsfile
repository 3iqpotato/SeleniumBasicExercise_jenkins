pipeline {
    agent any

    stages {
        stage('Setup ChromeDriver') {
            steps {
                script {
                    if (isUnix()) {
                        // Linux setup
                        sh '''
                            sudo apt-get update -y
                            sudo apt-get install -y wget unzip google-chrome-stable
                            CHROME_DRIVER_VERSION=$(curl -s https://chromedriver.storage.googleapis.com/LATEST_RELEASE)
                            wget -N https://chromedriver.storage.googleapis.com/${CHROME_DRIVER_VERSION}/chromedriver_linux64.zip
                            unzip chromedriver_linux64.zip -d /usr/local/bin/
                            chmod +x /usr/local/bin/chromedriver
                        '''
                    } else {
                        // Windows setup (fixed version)
                        bat '''
                            choco install googlechrome -y
                            curl -L -o chromedriver.zip https://chromedriver.storage.googleapis.com/LATEST_RELEASE/chromedriver_win32.zip
                            mkdir "%ProgramFiles%\\ChromeDriver" || echo Folder exists
                            powershell -command "Expand-Archive -Path chromedriver.zip -DestinationPath '%ProgramFiles%\\ChromeDriver' -Force"
                            set PATH=%PATH%;%ProgramFiles%\\ChromeDriver
                        '''
                    }
                }
            }
        }

stage('Run Tests') {
    steps {
        checkout scm
        script {
            if (isUnix()) {
                sh 'dotnet test --configuration Release --logger "trx;LogFileName=testresults.trx"'
            } else {
                bat 'dotnet test --configuration Release --logger "trx;LogFileName=testresults.trx"'
            }
        }
        // Convert TRX to JUnit format or use MSTest plugin
        junit '**/testresults.trx' 
    }
}
    }

    post {
        always {
            cleanWs()
        }
    }
}