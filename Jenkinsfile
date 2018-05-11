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
        stage('build && SonarQube analysis') {
            steps {
                // Optionally use a Maven environment you've configured already
                withMaven(maven:'Maven 3.5') {
                    sh 'mvn clean package sonar:sonar'
                }
            }
        }
         stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    // true = set pipeline to UNSTABLE, false = don't
                    // Requires SonarQube Scanner for Jenkins 2.7+
                    waitForQualityGate abortPipeline: true
                }
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
