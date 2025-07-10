pipeline {
  agent any

  environment {
    GIT_REPO = 'https://github.com/harishnshetty/hello-worl-jar.git'
    GIT_BRANCH = 'main'
    MAVEN_REPO_ID = 'nexus' // Used if deploying via settings.xml (not required here)
    MAVEN_REPO_URL = 'http://nexus:8081/repository/my-maven-hosted/'
  }

  tools {
    maven 'maven'   // Ensure 'maven' is defined in Jenkins global tools
    jdk 'JDK17'     // Ensure 'JDK17' is defined in Jenkins global tools
  }

  stages {

    stage('Clone Repository') {
      steps {
        echo "Cloning from ${env.GIT_REPO} (${env.GIT_BRANCH})"
        git url: "${env.GIT_REPO}", branch: "${env.GIT_BRANCH}"
      }
    }

    stage('Build with Maven') {
      steps {
        echo "Using Java version:"
        sh 'java -version'

        echo "Building the project with Maven"
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
        script {
          // Validate that the .jar file exists
          def jarFile = 'target/maven-jar-sample-1.0-SNAPSHOT.jar'
          if (fileExists(jarFile)) {
            nexusArtifactUploader(
              nexusVersion: 'nexus3',
              protocol: 'http',
              nexusUrl: 'localhost:8081',
              groupId: 'com.yhayashi30.sample',
              version: '1.0-SNAPSHOT',
              repository: 'my-maven-hosted',
              credentialsId: 'nexus',
              artifacts: [
                [
                  artifactId: 'maven-jar-sample',
                  classifier: '',
                  file: jarFile,
                  type: 'jar'
                ]
              ]
            )
          } else {
            error "Artifact file not found: ${jarFile}"
          }
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
