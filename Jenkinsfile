pipeline {
  agent any

  environment {
    GIT_REPO = 'https://github.com/harishnshetty/hello-worl-jar.git'
    GIT_BRANCH = 'main'
  }

  tools {
    maven 'maven'   // Name must match Global Tool Configuration
    jdk 'JDK17'     // Correct case-sensitive match
  }

  stages {
    stage('Clone Repository') {
      steps {
        git url: "${env.GIT_REPO}", branch: "${env.GIT_BRANCH}"
      }
    }

    stage('Build with Maven') {
      steps {
        sh 'java -version'          // Verify correct JDK
        sh 'mvn clean install'      // Build
      }
    }

    stage('Archive Artifacts') {
      steps {
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }
  }

  post {
    success {
      echo '✅ Build and archive successful.'
    }
    failure {
      echo '❌ Build failed.'
    }
  }
}
