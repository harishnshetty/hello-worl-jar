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
        withCredentials([usernamePassword(credentialsId: "${env.MAVEN_REPO_ID}", usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
          sh """
            mvn deploy -DskipTests \
              -DaltDeploymentRepository=${env.MAVEN_REPO_ID}::default::${env.MAVEN_REPO_URL} \
              -Dusername=$NEXUS_USER -Dpassword=$NEXUS_PASS
          """
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
