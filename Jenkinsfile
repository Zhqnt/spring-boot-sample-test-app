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
        echo 'stage deploy'
        bat 'javaw -jar target/testing-web-complete.jar'
        echo 'end of deploy'
        input(message: 'voulez vous continuer ? ', ok: 'Lets go')
      }
    }

  }
  tools {
    maven 'maven 3.9'
    jdk 'java 11'
  }
}