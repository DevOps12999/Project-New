pipeline {
    agent any
    
    parameters {
        string(name: 'BRANCH_SPECIFIER', defaultValue: '*/main', description: 'Branch to build')
        string(name: 'URL', defaultValue: '', description: 'Branch to build')
        
    }
    
    triggers {
        pollSCM('* * * * *')  // Poll every minute; adjust the cron expression as needed
        cron('0 1 * * *')    // Build every day at 1 AM; adjust the cron expression as needed
    }
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM', 
                              branches: [[name: params.BRANCH_SPECIFIER]], 
                              userRemoteConfigs: [[url: '$URL']]])
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    def mavenHome = tool name: 'Maven_Home', type: 'hudson.tasks.Maven$MavenInstallation'
                    sh "${mavenHome}/bin/mvn clean install"
                }
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def mavenHome = tool name: 'Maven_Home', type: 'hudson.tasks.Maven$MavenInstallation'
                    withSonarQubeEnv('SonarQubeServer') {
                        sh "${mavenHome}/bin/mvn sonar:sonar -Dsonar.projectKey=sonar"
                    }
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                script {
                    def tomcatHome = '/path/to/tomcat'  // Provide the path to your Tomcat installation
                    def warFile = 'target/my-app.war'   // Replace with the actual path to your WAR file
                    
                    sh "cp ${warFile} ${tomcatHome}/webapps/"
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
