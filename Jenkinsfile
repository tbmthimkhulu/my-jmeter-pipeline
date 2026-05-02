pipeline {
    agent any

    stages {
        stage('Run JMeter Test') {
            steps {
                bat 'jmeter -n -t Hello.jmx -l result.jtl'
            }
        }
    }
}