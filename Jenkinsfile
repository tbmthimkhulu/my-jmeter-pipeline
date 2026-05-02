pipeline {
    agent any

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
                                script: 'curl -s http://localhost:9090/api/hello',
                                returnStatus: true
                            )
                            return (status == 0)
                        }
                    }
                }
            }
        }

        stage('Run JMeter Test') {
            steps {
                echo 'Running JMeter test...'
                bat 'jmeter -n -t Hello.jmx -l result.jtl -e -o report'
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
            echo 'Pipeline finished.'
        }
        success {
            echo 'SUCCESS: Pipeline executed successfully!'
        }
        failure {
            echo 'FAILURE: Check logs above.'
        }
    }
}