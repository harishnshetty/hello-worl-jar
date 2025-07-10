pipeline {
  agent any

  environment {
    GIT_REPO = 'https://github.com/harishnshetty/hello-worl-jar.git'
    GIT_BRANCH = 'main'
    MAVEN_REPO_ID = 'nexus' // Custom ID used only in Jenkins
    MAVEN_REPO_URL = 'http://nexus:8081/repository/my-maven-hosted/'
  }

  tools {
    maven 'maven'
    jdk 'JDK17'
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
        nexusArtifactUploader artifacts: [[artifactId: 'maven-jar-sample', classifier: '', file: 'target/maven-jar-sample-1.0-SNAPSHOT.jar', type: '.jar']], credentialsId: 'nexus', groupId: 'com.yhayashi30.sample', nexusUrl: 'localhost:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'my-maven-hosted', version: '1.0-SNAPSHOT'
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
