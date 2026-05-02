pipeline {
    agent any

    environment {
        JMETER_HOME = "C:\\apache-jmeter-5.x"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://your-repo-url.git'
            }
        }

        stage('Compile Server') {
            steps {
                bat 'javac SimpleServer.java'
                // Compiles your Java server
            }
        }

        stage('Start Server') {
            steps {
                bat 'start /B java SimpleServer'
                // Starts server in background
            }
        }

        stage('Wait for Server') {
            steps {
                bat 'timeout /t 5'
                // Wait for server to fully start
            }
        }

        stage('Run JMeter Test') {
            steps {
                bat '%JMETER_HOME%\\bin\\jmeter -n -t Hello.jmx -l results.jtl -e -o report'
                // Runs performance test
            }
        }

        stage('Archive Report') {
            steps {
                archiveArtifacts artifacts: 'report/**', fingerprint: true
            }
        }
    }
}