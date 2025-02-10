pipeline {
    agent any

    environment {
        CHROME_VERSION = '133.0.6943.60'
        CHROMEDRIVER_VERSION = '133.0.6943.60'
        CHROME_INSTALL_PATH = 'C:\\Program Files\\Google\\Chrome\\Application'
        CHROMEDRIVER_PATH = '"C:\\Program Files\\Google\\Chrome\\Application\\chromedriver.exe"'
    }

        stage('Install Chocolatey') {
            steps {
                bat '''
                REM Install Chocolatey if it's not installed
                SET CHOCO_PATH="C:\\ProgramData\\chocolatey\\bin\\choco.exe"
                IF NOT EXIST %CHOCO_PATH% (
                    echo Installing Chocolatey...
                    SET-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
                    Invoke-Expression ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
                ) ELSE (
                    echo Chocolatey is already installed.
                )
                '''
            }
        }


        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Uninstall Chrome') {
            steps {
                bat 'if exist "C:\\Program Files\\Google\\Chrome\\Application\\chrome.exe" (echo Uninstalling Chrome && "C:\\Program Files\\Google\\Chrome\\Application\\chrome_installer.exe" /uninstall)'
            }
        }

        stage('Install Specific Version of Chrome') {
            steps {
                bat '''
                echo Installing Chrome version $CHROME_VERSION
                choco install googlechrome --version $CHROME_VERSION --confirm
                '''
            }
        }

        stage('Install ChromeDriver') {
            steps {
                bat '''
                echo Installing ChromeDriver version $CHROMEDRIVER_VERSION
                curl -o "C:\\Program Files\\Google\\Chrome\\Application\\chromedriver.zip" https://chromedriver.storage.googleapis.com/$CHROMEDRIVER_VERSION/chromedriver_win32.zip
                tar -xf "C:\\Program Files\\Google\\Chrome\\Application\\chromedriver.zip" -C "C:\\Program Files\\Google\\Chrome\\Application\\"
                '''
            }
        }

        stage('Set Up .NET Core') {
            steps {
                bat '''
                echo Installing .NET Core SDK 6.0
                choco install dotnet-sdk --version 6.0.100
                '''
            }
        }

        stage('Restore Dependencies') {
            steps {
                bat 'dotnet restore SeleniumIde.sln'
            }
        }

        stage('Build') {
            steps {
                bat 'dotnet build SeleniumIde.sln --configuration Release'
            }
        }

        stage('Run Tests') {
            steps {
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
