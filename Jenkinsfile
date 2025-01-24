pipeline {
  agent any
  tools {
    maven 'Maven_3_8_7'
  }

/* SAST - static Code Analysys */	
/*  stages {
    stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh 'mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=$SONAR_TOKEN -Dsonar.projectKey=easybuggy -Dsonar.host.url=http://localhost:9000/'
        }
      }
    }*/
    stage('Build') {
      steps {
	  withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("charanrajkumar9/testeb")
          }
        }
      }
    }



/* Container Scan using SNYK */
  /*  stage('RunContainerScan') {
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
    }*/

/* SCA scan using snyk as docker container to scan docker image */
	  stage ('RunContainerScan'){
	    steps{
		 script{
		   snykSecurity severity: 'critical', snykInstallation: 'snyk', snykTokenId: 'snyk-cred'
		   def variable = sh(
			   script:'snyk container test charanrajkumar9/testeb',
			   returnStatus: true)

		  echo "error code = ${variable}"
		  if (variable !=0){
			  echo "Alert for vulnerablility found"
		        }	 	 
		      }   
	       } 	  
	  }

/* SCA Scan using SNYK */
    stage('RunSnykSCA') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          sh 'mvn snyk:test -fn'   // -fn flag will prevent the build from failing
        }
      }
    }

/* DAST - Dynamic Application Security Testing Code Analysys */
    /*stage('RunDASTUsingZAP') {
      steps {
      //  bat("C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout //C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\Output.html")
	 sh 'cd /opt/owaspzap/zap/' 
         sh './zap.sh -port 9393 -cmd -quickurl https://www.example.com -quickprogress -quickout /opt/owaspzap/zap/Output.html'
      }
    }*/
	  
/* IAC security scan using checkov */
    stage('checkov') {
      steps {
        sh 'checkov -s -f main.tf'  // -s is used to prevent build from failing and -f is used to pass any file
      }
    }

  }
}
