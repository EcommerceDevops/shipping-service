pipeline {
  agent any
  stages {
    stage('Hola GitHub') {
      steps {
        echo "✅ Webhook recibido desde GitHub 🎉"
      }
    }

    stage('Build & Test') {
      steps {
        echo "🔩 Dando permisos de ejecución al wrapper de Maven..."
        sh 'chmod +x mvnw'
        
        echo "🚀 Compilando el proyecto y ejecutando pruebas (esto generará el reporte de cobertura)..."
        
        configFileProvider([configFile(fileId: 'private-maven-settings', variable: 'MAVEN_SETTINGS_FILE')]) {
            echo "El archivo settings.xml temporal está en: ${env.MAVEN_SETTINGS_FILE}"
            sh "./mvnw --settings ${env.MAVEN_SETTINGS_FILE} clean verify"
        }
        
      }
    }

    stage('SonarQube analysis') {
      steps {
        withSonarQubeEnv('SonarQube') {
          script {
            def scannerHome = tool 'SonarQube Scanner'
            sh "${scannerHome}/bin/sonar-scanner -X"
          }
        }
      }
    }


    stage('Quality Gate') {
      steps {
        timeout(time: 5, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }

  }
}
