# Jenkins Pipeline Setup

This document provides instructions for setting up the Jenkins pipeline for the Java CI Demo application.

## Prerequisites

### Jenkins Configuration
1. **Jenkins Version**: 2.400+ recommended
2. **Required Plugins**:
   - Pipeline Plugin
   - Maven Integration Plugin
   - JUnit Plugin
   - Checkstyle Plugin (optional)
   - Git Plugin

### Tool Configuration in Jenkins
Configure the following tools in Jenkins Global Tool Configuration:

#### Maven
- **Name**: `Maven-3.9.0` (or update Jenkinsfile accordingly)
- **Version**: Maven 3.9.0 or later
- **Installation**: Automatic installation from Apache

#### JDK
- **Name**: `JDK-17` (or update Jenkinsfile accordingly)
- **Version**: OpenJDK 17 or later
- **Installation**: Automatic installation

## Pipeline Stages

The Jenkins pipeline includes the following stages:

1. **Checkout**: Retrieves source code from SCM
2. **Build**: Compiles the Java source code using `mvn clean compile`
3. **Test**: Runs unit tests using `mvn test`
4. **Package**: Creates JAR file using `mvn package -DskipTests`
5. **Archive Artifacts**: Archives the generated JAR file
6. **Code Quality Analysis**: Runs checkstyle checks (optional)

## Setting Up the Pipeline

### Option 1: Pipeline from SCM
1. Create a new Pipeline job in Jenkins
2. In the Pipeline section, select "Pipeline script from SCM"
3. Configure your Git repository URL
4. Set Script Path to `Jenkinsfile`

### Option 2: Direct Pipeline Script
1. Create a new Pipeline job in Jenkins
2. Copy the contents of `Jenkinsfile` into the Pipeline script section

## Customization

### Adjusting Tool Versions
Update the `tools` section in the Jenkinsfile:
```groovy
tools {
    maven 'Your-Maven-Version'
    jdk 'Your-JDK-Version'
}
```

### Windows vs Linux
The current Jenkinsfile uses `bat` commands for Windows. For Linux agents, change:
- `bat 'mvn clean compile'` → `sh 'mvn clean compile'`
- `bat 'mvn test'` → `sh 'mvn test'`
- `bat 'mvn package -DskipTests'` → `sh 'mvn package -DskipTests'`
- `bat 'mvn checkstyle:check'` → `sh 'mvn checkstyle:check'`

### Adding Notifications
Add notification steps in the `post` section:
```groovy
post {
    success {
        emailext (
            subject: "Build Success: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
            body: "Build completed successfully!",
            to: "team@example.com"
        )
    }
    failure {
        emailext (
            subject: "Build Failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
            body: "Build failed. Please check the logs.",
            to: "team@example.com"
        )
    }
}
```

## Expected Artifacts

After successful pipeline execution, the following artifacts will be available:
- **JAR File**: `target/java-ci-demo05-0.0.1-SNAPSHOT.jar`
- **Test Reports**: `target/surefire-reports/*.xml`
- **Checkstyle Reports**: `target/checkstyle-result.xml` (if configured)

## Troubleshooting

### Common Issues
1. **Tool not found**: Ensure Maven and JDK are properly configured in Jenkins Global Tool Configuration
2. **Permission issues**: Ensure Jenkins has proper file system permissions
3. **Memory issues**: Adjust `MAVEN_OPTS` in the environment section if needed

### Logs
Check the Jenkins console output for detailed error messages and build logs.
