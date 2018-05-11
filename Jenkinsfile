pipeline {
    agent {
        docker {
            image 'node:6-alpine' 
            args '-p 3000:3000' 
        }
    }
    environment {
        CI = 'true'
    }
    stages {
        stage('Build') { 
            steps {
                sh 'npm cache clean'
                sh 'npm install --no-bin-links --save || npm install --no-bin-links --save' 
            }
        }
        stage('Test') {
            steps {
                sh 'npm install react-scripts -g'
                sh 'npm install --no-bin-links --save'
                sh './jenkins/scripts/test.sh'
            }
        }
         stage('SonarQube analysis') {
         withSonarQubeEnv('My SonarQube Server') {
            // requires SonarQube Scanner for Maven 3.2+
            sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.2:sonar'
            }
         }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
                input message: 'Finished using the web site? (Click "Proceed" to continue)'
                sh './jenkins/scripts/kill.sh'
            }
        }
    }
}
