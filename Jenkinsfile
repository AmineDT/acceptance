pipeline {
    agent any
    stages {
        stage('Compilation') {
            steps {
                sh './gradlew compileJava'
            }
        }
        stage('test unitaire') {
            steps {
                sh './gradlew test'
            }
        }
        stage('Code coverage') {
            steps {
                sh './gradlew jacocoTestReport'
                publishHTML(target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: 'JaCoCo Report'
                    ])
                sh './gradlew jacocoTestCoverageVerification'
            }
        }
        stage('Package') {
            steps {
                sh './gradlew build'
            }
        }
        stage('Docker build') {
            steps {
                sh 'docker build -t calculator .'
            }
        }
        stage('Docker push') {
            steps {
                sh 'docker push localhost:5000/calculator'
            }
        }
        stage('Déploiement sur staging') {
            steps {
                sh 'docker run -d --rm -p 8765:8081 --name calculator localhost:5000/calculator'
            }
        }
        stage("Test d'acceptation") {
            steps {
                sleep 60
                sh './gradlew acceptanceTest -Dcalculator.url=http://localhost:8765'
            }
        }
    }
    post {
        always {
            sh 'docker stop calculator'
        }
    }
}
