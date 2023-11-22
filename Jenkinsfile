pipeline {
  agent any
  tools {
    maven 'Maven_3_8_7'
  }

  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh 'mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=$SONAR_TOKEN -Dsonar.projectKey=easybuggy -Dsonar.host.url=http://192.168.56.31:9000/'
        }
      }
    }
    stage('Build') {
      steps {
	  withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("charanrajkumar9/testeb")
          }
        }
      }
    }
    stage('RunContainerScan') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              //bat("C:\\snyk\\snyk-win.exe  container test charanrajkumar9/testeb")
			  sh 'snyk auth f1931d4b-f45e-4132-bbab-5d921e71279c'
			  sleep(time:5,unit:"SECONDS")
			  sh 'snyk container test charanrajkumar9/testeb' 
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }
    stage('RunSnykSCA') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          sh 'mvn snyk:test -fn'   // -fn flag will prevent the build from failing
        }
      }
    }
   /* stage('RunDASTUsingZAP') {
      steps {
      //  bat("C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout //C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\Output.html")
	  
	         sh './opt/owaspzap/zap/zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout /opt/owaspzap/zap/Output.html'
	  
	  
      }
    }*/

    stage('checkov') {
      steps {
        sh 'checkov -s -f main.tf'
      }
    }

  }
}
