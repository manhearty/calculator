pipeline {
     environment {
        registry = "manhearty/calculator"
        registryCredential = 'DOCKER'
        dockerImage = ''
     }
     agent any
     triggers{
         pollSCM('* * * * *')
     }
     stages {
          stage("Compile") {
               steps {
                    sh "./gradlew compileJava"
               }
          }
          stage("Unit test") {
               steps {
                    sh "./gradlew test"
               }
          }

          stage("Code coverage") {
               steps {
                    sh "./gradlew jacocoTestReport"
                    publishHTML (target: [
                        reportDir: 'build/reports/jacoco/test/html',
                        reportFiles: 'index.html',
                        reportName: "JaCoCo Report"
                    ])
                    sh "./gradlew jacocoTestCoverageVerification"
               }
          }
          stage("Static code analysis") {
               steps {
                    sh "./gradlew checkstyleMain"
          }
          }
          stage("Package"){
               steps {
                 sh "./gradlew build"
              }
          }
	
          stage('Building image') {
             steps{
                script {
                   dockerImage = docker.build registry + ":$BUILD_NUMBER"
                  }
              }
          }

          stage('Deploy Image') {
            steps{
               script {
                   docker.withRegistry( '', registryCredential ) {
                   dockerImage.push()
                 }
          }
          }
    }
            
  }        
}
