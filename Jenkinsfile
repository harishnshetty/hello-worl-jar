pipeline {
    agent any

    environment {
        GIT_REPO = 'https://github.com/harishnshetty/hello-worl-jar.git'
        GIT_BRANCH = 'main'
    }

    tools {
        maven 'maven'
        jdk 'jdk'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "${env.GIT_REPO}", branch: "${env.GIT_BRANCH}"
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'java -version'
                sh 'mvn clean install'
            }
        }

        stage('Publish Test Reports') {
            steps {
                junit 'target/surefire-reports/*.xml'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withMaven(globalMavenSettingsConfig: 'settings.xml', jdk: 'jdk', maven: 'maven', mavenSettingsConfig: '', traceability: true) {
                    // some block
                    sh 'mvn deploy'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build and deploy successful.'
        }
        failure {
            echo '❌ Build failed.'
        }
    }
}