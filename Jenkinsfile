
//dev pipeline
pipeline
{
	
   agent any
   tools
   {
      maven "Maven 3.9.6"
   }
   stages
   {
           stage('git checkout')
           {
              steps
              {
                  notifyBuild('STARTED') 
                 git branch:'dev', url:'https://github.com/KKdevopsb7test/maven-webapplication-project-kkfunda.git'
              }
           }
           stage('compile')
           {
              steps
              {
                 sh "mvn compile"
              }
           }
           stage('Build')
           {
             steps
             {
               sh "mvn clean package"
             }
           }
           stage('SQ REPORT')
           {
             steps
             {
                sh "mvn sonar:sonar"
             }
           }
           stage('Deploy to nexus')
           {
              steps
              {
                sh "mvn clean deploy"
              }
           }
           stage('Deploy to tomcat')
           {
              steps
              {
                 sh """

 curl -u kk:password \
--upload-file /var/lib/jenkins/workspace/scripted-way-pipeline/target/maven-web-application.war \
"http://3.106.109.162:8080/manager/text/deploy?path=/maven-web-application&update=true"
          
        """
              }
           }
		   
		   stage('bsnl-qa')
		   {
		   build job : 'bsnl-qa'  //this is downstream job
		   }

   }  //stages ending

post {
  success {

    script
    {
     notifyBuild(currentBuild.result)
    }
    
  }
  failure {

  script
  {
    notifyBuild(currentBuild.result)

  }
   
  }
}  

} //pipeline ending



// Notification method
def notifyBuild(String buildStatus = 'STARTED') {
    buildStatus = buildStatus ?: 'SUCCESS'

    def colorCode
    def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
    def summary = "${subject} (${env.BUILD_URL})"

    switch (buildStatus) {
        case 'STARTED':
            colorCode = '#FFFF00' // Yellow
            break
        case 'SUCCESS':
            colorCode = '#00FF00' // Green
            break
        default:
            colorCode = '#FF0000' // Red
    }

    slackSend(color: colorCode, message: summary)
}
