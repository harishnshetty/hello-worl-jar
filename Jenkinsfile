pipeline {
  agent any

  environment {
    GIT_REPO = 'https://github.com/harishnshetty/hello-worl-jar.git'
    GIT_BRANCH = 'main'
    MAVEN_REPO_ID = 'nexus' // Matches <id> in pom.xml and Jenkins credentials ID
    MAVEN_REPO_URL = 'http://localhost:8081/repository/my-maven-hosted/'
  }

  tools {
    maven 'maven'    // Must match Maven label in Global Tool Configuration
    jdk 'JDK17'      // Must match JDK label in Global Tool Configuration
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
        withCredentials([usernamePassword(credentialsId: "${env.MAVEN_REPO_ID}", usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
          sh """
            mvn deploy -DskipTests \
              -Dnexus.username=$NEXUS_USER \
              -Dnexus.password=$NEXUS_PASS \
              -DaltDeploymentRepository=${env.MAVEN_REPO_ID}::default::${env.MAVEN_REPO_URL}
          """
        }
      }
    }
  }

  post {
    success {
      echo '✅ Build, test, and deployment successful.'
    }
    failure {
      echo '❌ Build failed.'
    }
  }
}
