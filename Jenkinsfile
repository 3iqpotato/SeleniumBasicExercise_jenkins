pipeline {
    agent any  // Can now run on Linux or Windows

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
                            curl -L -o chromedriver.zip https://chromedriver.storage.googleapis.com/LATEST_RELEASE/chromedriver_win32.zip
                            tar -xf chromedriver.zip -C "%ProgramFiles%"
                            set PATH=%PATH%;%ProgramFiles%\\chromedriver.exe
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
}