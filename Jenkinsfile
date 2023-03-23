pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo 'build stage'
        bat 'mvnw -DskipTests clean install'
        echo 'end of build'
        archiveArtifacts '**/target/*.jar'
        echo 'fin archivage'
        bat 'docker build -t testapp:latest .'
      }
    }

    stage('test') {
      parallel {
        stage('test intégration') {
          steps {
            echo 'test d\'intégration'
            bat 'mvnw -Dtest=com.example.testingweb.integration.** test'
            echo 'end of integration test'
          }
        }

        stage('test fonctionnel') {
          steps {
            echo 'test fonctionnel'
            bat 'mvnw -Dtest=com.example.testingweb.functional.** test'
            echo 'end of functional test'
          }
        }

        stage('smoke test') {
          steps {
            echo 'smoke test'
            bat 'mvnw -Dtest=com.example.testingweb.smoke.** test'
            echo 'end of smoke test'
            junit '**/target/surefire-reports/TEST-*.xml'
          }
        }

      }
    }

    stage('deploy') {
      steps {
        input(message: 'voulez vous continuer ? ', ok: 'Lets go')
        echo 'stage deploy'
        bat 'docker run -d -p 3030:9090 testapp:latest'
        echo 'end of deploy'
      }
    }

  }
  tools {
    maven 'maven 3.9'
    jdk 'java 11'
  }
  post {
    success {
      emailext(to: 'phi.rouff@gmail.com', subject: "${env.BUILD_ID} - ${currentBuild.result}", body: "${env.BUILD_ID} - ${env.JENKINS_URL}")
    }

  }
}