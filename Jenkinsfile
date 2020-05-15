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

          stage("Deploy to Staging"){
               steps {
               sh "docker run -d --rm -p 8765:8080 --name calculator manhearty/calculator:$BUILD_NUMBER"
            }
          }

	  stage("Acceptance test"){
               steps {
               sleep 60
               sh "chmod +x acceptance_test.sh && ./acceptance_test.sh"
             }
         }

            
  }
 
           post {
              always {
                  sh "docker stop calculator"
             }

         }

        
}
