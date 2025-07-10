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
      writeFile file: 'settings.xml', text: """
        <settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
          <servers>
            <server>
              <id>${env.MAVEN_REPO_ID}</id>
              <username>${NEXUS_USER}</username>
              <password>${NEXUS_PASS}</password>
            </server>
          </servers>
        </settings>
      """

      sh """
        mvn deploy -DskipTests \
          -DaltDeploymentRepository=${env.MAVEN_REPO_ID}::default::${env.MAVEN_REPO_URL} \
          --settings settings.xml
      """
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
