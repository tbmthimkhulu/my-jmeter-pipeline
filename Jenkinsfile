pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\apache-jmeter-5.6.3'   // adjust if needed
        REPORT_DIR = 'report'
        RESULT_FILE = 'result.jtl'
        PORT = '9090'
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Using code from GitHub (already checked out by Jenkins)'
            }
        }

        stage('Compile Server') {
            steps {
                echo 'Compiling Java server...'
                bat 'javac src\\SimpleServer.java'
            }
        }

        stage('Start Server') {
            steps {
                echo "Starting server on port ${PORT}..."
                bat 'start /B java -cp src SimpleServer'
            }
        }

        stage('Wait for Server') {
            steps {
                echo 'Waiting for server to be ready...'
                timeout(time: 1, unit: 'MINUTES') {
                    waitUntil {
                        script {
                            def response = bat(
                                script: "curl -s http://localhost:${PORT}/api/hello",
                                returnStdout: true
                            ).trim()
                            return response.contains("Hello")
                        }
                    }
                }
            }
        }

        stage('Run JMeter Test') {
            steps {
                echo 'Running JMeter test...'
                bat """
                IF EXIST %REPORT_DIR% rmdir /s /q %REPORT_DIR%
                IF EXIST %RESULT_FILE% del %RESULT_FILE%

                ${JMETER_HOME}\\bin\\jmeter -n -t Hello.jmx -l %RESULT_FILE% -e -o %REPORT_DIR%
                """
            }
        }

        stage('Archive Report') {
            steps {
                echo 'Archiving JMeter HTML report...'
                archiveArtifacts artifacts: 'report/**', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Cleaning up... Stopping Java server'
            bat 'taskkill /F /IM java.exe || exit 0'
        }

        success {
            echo 'SUCCESS: Pipeline executed successfully!'
        }

        failure {
            echo 'FAILURE: Check logs above.'
        }
    }
}