pipeline {
    agent any
    
    tools {
        maven 'Maven-3.9.12' // Adjust version as needed
        jdk 'JDK-21' // Adjust version as needed
    }
    
    environment {
        MAVEN_OPTS = '-Xmx1024m'
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building the project...'
                bat 'mvn clean compile'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running tests...'
                bat 'mvn test'
            }
            // post {
            //     always {
            //         // Publish test results
            //         publishTestResults testResultsPattern: 'target/surefire-reports/*.xml'
            //         // Archive test reports
            //         archiveArtifacts artifacts: 'target/surefire-reports/*.xml', allowEmptyArchive: true
            //     }
            // }
        }
        
        stage('Package') {
            steps {
                echo 'Packaging the application...'
                bat 'mvn package -DskipTests'
            }
        }
        
        stage('Run Application') {
            steps {
                echo 'Running the Spring Boot application...'
                script {
                    // Run the application in background for a short time to verify it starts
                    def process = bat(script: 'start /B java -jar target\\java-ci-demo05-0.0.1-SNAPSHOT.jar', returnStatus: true)
                    
                    // Wait a few seconds for the application to start
                    sleep(time: 10, unit: 'SECONDS')
                    
                    // Test if the application is responding
                    try {
                        bat 'curl -f http://localhost:9090/ || echo "Application health check failed"'
                        echo 'Application started successfully and is responding!'
                    } catch (Exception e) {
                        echo "Health check failed: ${e.getMessage()}"
                    }
                    
                    // Stop the application
                    bat 'taskkill /F /IM java.exe || echo "No java processes to kill"'
                }
            }
        }
        
        // stage('Archive Artifacts') {
        //     steps {
        //         echo 'Archiving artifacts...'
        //         archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: false
        //     }
        // }
        
        // stage('Code Quality Analysis') {
        //     steps {
        //         echo 'Running code quality checks...'
        //         bat 'mvn checkstyle:check'
        //     }
            // post {
            //     always {
            //         // Archive checkstyle reports if they exist
            //         archiveArtifacts artifacts: 'target/checkstyle-result.xml', allowEmptyArchive: true
            //     }
            // }
       // }
    }
    
    post {
        always {
            echo 'Pipeline execution completed.'
            // Clean workspace
            cleanWs()
        }
        success {
            echo 'Pipeline executed successfully!'
            // You can add notifications here (email, Slack, etc.)
        }
        failure {
            echo 'Pipeline failed!'
            // You can add failure notifications here
        }
        unstable {
            echo 'Pipeline is unstable!'
        }
    }
}
