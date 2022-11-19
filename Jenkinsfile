node {
  def WORKSPACE = "C:/ProgramData/Jenkins/.jenkins/workspace/sonarqube-testing>"
  def dockerImageTag = "sonarqube-testing${env.BUILD_NUMBER}"

  try{
    // notifyBuild('STARTED')
    stage('Clone Repo') {
      // for display purposes
      // Get some code from a GitHub repository
      git branch: 'main', url: 'https://github.com/Devsharma27/sonarqube-testing.git'
    }

    stage('Compile-Package'){
      //get maven home path 
      def mvnhome = tool name: 'MAVEN_HOME', type: 'maven'
      bat "${mvnhome}/bin/mvn package"
    }
            
    stage('SonarQube Analysis') {
      def mvnhome = tool name: 'MAVEN_HOME', type: 'maven'
      withSonarQubeEnv('sonarqube-token') {
        bat "${mvnHome}/bin/mvn sonar:sonar"
      }
    }
          
    stage('Build docker') {
      dockerImage = docker.build("sonarqube-deploy:${env.BUILD_NUMBER}")
    }

    stage('Deploy docker'){
        echo "Docker Image Tag Name: ${dockerImageTag}"
        bat "docker stop sonarqube-deploy || (exit 0) && docker rm sonarqube-deploy || (exit 0)"
        bat "docker run --name sonarqube-deploy -d -p 8081:8081 sonarqube-deploy:${env.BUILD_NUMBER}"
    }
  }catch(e){

//currentBuild.result = "FAILED"
    throw e
    }finally{
//notifyBuild(currentBuild.result)
    }
}

def notifyBuild(String buildStatus = 'STARTED'){

// build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'
  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def now = new Date()
  // message
  def subject = "${buildStatus}, Job: ${env.JOB_NAME} FRONTEND - Deployment Sequence: [${env.BUILD_NUMBER}] "
  def summary = "${subject} - Check On: (${env.BUILD_URL}) - Time: ${now}"
  def subject_email = "Spring boot Deployment"
  def details = """<p>${buildStatus} JOB </p>
    <p>Job: ${env.JOB_NAME} - Deployment Sequence: [${env.BUILD_NUMBER}] - Time: ${now}</p>
    <p>Check console output at "<a href="${env.BUILD_URL}">${env.JOB_NAME}</a>"</p>"""


  // Email notification
    emailext (
         to: "dev.sharma8989@gmail.com",
         subject: subject_email,
         body: details,
         recipientProviders: [[$class: 'DevelopersRecipientProvider']]
       )
}