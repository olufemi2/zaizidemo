pipeline {
    agent any
    stages {
      stage('Build') {
            steps {
                sh 'mvn -B package'
            }
        }
        stage('Test') {
            steps {
                echo 'Installing k6'
                sh 'sudo chmod +x setup_k6.sh'
                sh 'sudo ./setup_k6.sh'
                echo 'Running K6 performance tests...'
                sh 'k6 run loadtests/performance-test.js'
            }
        }
    }
}
