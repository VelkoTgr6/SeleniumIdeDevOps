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

                // Checkout the code from the repository

                checkout scm
            }
        }

        stage('Set Up .NET Core') {
            steps {

                // Set up .NET SDK

                bat '''
                echo Installing .NET Core SDK 6.0
                choco install dotnet-sdk --version 6.0.100
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
