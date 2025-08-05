pipeline {
    agent {
        label 'windows' // Requires Windows agent with .NET SDK installed
    }
    
    environment {
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        NUGET_PATH = 'C:\\Program Files\\NuGet\\nuget.exe'
        SOLUTION_FILE = 'YourSolution.sln' // Update with your solution file
        TEST_PROJECT = 'YourTestProject\\YourTestProject.csproj' // Update with your test project path
        CHROME_DRIVER_PATH = 'C:\\SeleniumWebDrivers\\ChromeDriver' // Update with your ChromeDriver path
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm // Checks out the source code from SCM
            }
        }
        
        stage('Restore NuGet Packages') {
            steps {
                bat "\"${NUGET_PATH}\" restore \"${SOLUTION_FILE}\""
            }
        }
        
        stage('Build Solution') {
            steps {
                bat "dotnet build \"${SOLUTION_FILE}\" --configuration Release --no-restore"
            }
        }
        
        stage('Install ChromeDriver') {
            steps {
                script {
                    // Check if ChromeDriver is installed, if not install it
                    if (!fileExists("${CHROME_DRIVER_PATH}\\chromedriver.exe")) {
                        bat """
                            mkdir "${CHROME_DRIVER_PATH}" || echo Directory already exists
                            curl -L -o "${CHROME_DRIVER_PATH}\\chromedriver_win32.zip" https://chromedriver.storage.googleapis.com/LATEST_RELEASE/chromedriver_win32.zip
                            powershell Expand-Archive -Path "${CHROME_DRIVER_PATH}\\chromedriver_win32.zip" -DestinationPath "${CHROME_DRIVER_PATH}"
                            del "${CHROME_DRIVER_PATH}\\chromedriver_win32.zip"
                        """
                    }
                    
                    // Add ChromeDriver to PATH
                    withEnv(["PATH+CHROME_DRIVER=${CHROME_DRIVER_PATH}"]) {
                        bat "set PATH"
                    }
                }
            }
        }
        
        stage('Run Selenium Tests') {
            steps {
                script {
                    try {
                        // Run tests with logging
                        bat """
                            dotnet test \"${TEST_PROJECT}\" --configuration Release --no-build --logger "trx;LogFileName=testresults.trx" --results-directory \"${WORKSPACE}\\TestResults\"
                        """
                    } catch (e) {
                        // Test failures will throw an exception, but we want to continue to process results
                        echo "Some tests failed. Continuing to process test results..."
                        currentBuild.result = 'UNSTABLE'
                    }
                }
            }
            post {
                always {
                    // Publish test results regardless of test failures
                    publishTestResults allowEmptyResults: true, testResults: 'TestResults/*.trx'
                    
                    // Archive test results
                    archiveArtifacts artifacts: 'TestResults/*.trx', allowEmptyArchive: true
                }
            }
        }
    }
    
    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }
        success {
            echo 'Build and tests completed successfully!'
        }
        failure {
            echo 'Build or tests failed. Please investigate.'
        }
    }
}