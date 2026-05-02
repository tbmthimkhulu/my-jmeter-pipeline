pipeline {
    agent any

    environment {
        JMETER_HOME = 'C:\\apache-jmeter-5.6.3\\bin'
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Using code already checked out by Jenkins'
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
                echo 'Starting server on port 9090...'
                bat 'start /B java -cp src SimpleServer'
            }
        }

        stage('Wait for Server') {
            steps {
                echo 'Waiting for server to be ready...'
                timeout(time: 60, unit: 'SECONDS') {
                    waitUntil {
                        script {
                            def status = bat(
                                script: 'powershell -Command "try { (Invoke-WebRequest http://localhost:9090/api/hello).StatusCode } catch { exit 1 }"',
                                returnStatus: true
                            )
                            return (status == 0)
                        }
                    }
                }
            }
        }

        stage('Clean Previous Results') {
            steps {
                echo 'Cleaning old results...'
                bat 'IF EXIST report rmdir /s /q report'
                bat 'IF EXIST result.jtl del result.jtl'
            }
        }

        stage('Run JMeter Test') {
            steps {
                echo 'Running JMeter test...'
                bat "\"%JMETER_HOME%\\jmeter.bat\" -n -t Hello.jmx -l result.jtl -e -o report"
            }
        }

        stage('Archive Report') {
            steps {
                echo 'Archiving HTML report...'
                archiveArtifacts artifacts: 'report/**', fingerprint: true
            }
        }
    }

    post {
        always {
            echo 'Stopping Java server...'
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