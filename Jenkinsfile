pipeline {
    agent any

    environment {
        CHROME_VERSION = '133.0.6943.60'
        CHROMEDRIVER_VERSION = '133.0.6943.60'
        CHROME_INSTALL_PATH = 'C:\\Program Files\\Google\\Chrome\\Application'
        CHROMEDRIVER_PATH = '"C:\\Program Files\\Google\\Chrome\\Application\\chromedriver.exe"'
    }

    stages {
        stage('Checkout Code') {
            steps {

                // Checkout the code from the reposotory

                checkout scm
            }
        }

        stage('Set Up .NET Core') {
            steps {
                // Set up .NET SDK
                bat '''
                echo Installing .NET Core SDK 6.0
                powershell -command "Invoke-WebRequest -Uri https://download.visualstudio.microsoft.com/download/pr/7e1e2b4d-9b2b-4b8e-8b2b-4b8e8b2b4b8e/dotnet-sdk-6.0.100-win-x64.exe -OutFile dotnet-sdk-6.0.100-win-x64.exe"
                powershell -command "Start-Process -FilePath dotnet-sdk-6.0.100-win-x64.exe -ArgumentList '/quiet' -Wait"
                '''
            }
        }

        stage('Uninstall Current Chrome') {
            steps {
                // Uninstall the current version of Chrome
                bat '''
                echo Uninstalling current Google Chrome
                powershell -command "Get-WmiObject -Class Win32_Product | Where-Object { $_.Name -eq 'Google Chrome' } | ForEach-Object { $_.Uninstall() }"
                '''
            }
        }

        stage('Install Specific Chrome Version') {
            steps {
                // Install the specific version of Chrom
                bat '''
                echo Installing Google Chrome version %CHROME_VERSION%
                powershell -command "Invoke-WebRequest -Uri https://dl.google.com/release2/chrome/AQAAAPy0j6k3nQAAAAAABQAAAAA/133.0.6943.60_chrome_installer.exe -OutFile chrome_installer.exe"
                powershell -command "Start-Process -FilePath chrome_installer.exe -ArgumentList '/silent /install' -Wait"
                '''
            }
        }

        stage('Download and Install ChromeDriver') {
            steps {
                bat '''
                echo Downloading ChromeDriver version %CHROMEDRIVER_VERSION%
                powershell -command "Invoke-WebRequest -Uri https://chromedriver.storage.googleapis.com/%CHROMEDRIVER_VERSION%/chromedriver_win32.zip -OutFile chromedriver.zip -UseBasicParsing"
                powershell -command "Expand-Archive -Path chromedriver.zip -DestinationPath ."
                powershell -command "Move-Item -Path .\\chromedriver.exe -Destination '%CHROME_INSTALL_PATH%\\chromedriver.exe' -Force"
                '''
            }
        }

        stage('Restore Dependencies') {
            steps {
               
                 // Restore .NET Core project dependencies

                 bat 'dotnet restore SeleniumIde.sln'
            }
        }

        stage('Build') {
            steps {
                
                 // Build the .NET Core project

                 bat 'dotnet build SeleniumIde.sln --configuration Release'
                
            }
        }

        stage('Run Tests') {
            steps {
               
                 // Run the tests using .NET Core

                 bat 'dotnet test SeleniumIde.sln --logger "trx;LogFileName=Testresults.trx"'
                
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/Testresults/*.trx', allowEmptyArchive: true
            junit '**/Testresults/*.trx'
        }
    }
}