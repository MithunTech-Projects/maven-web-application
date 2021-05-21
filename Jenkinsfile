node
{
	def mavenHome = tool name: "Maven-3.8.1" 
	
	properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
	
	
	stage('Checkout code')
	{
		git branch: 'development', credentialsId: 'GitID', url: 'https://github.com/MithunTech-Projects/maven-web-application.git'
	}
	
	stage('Build')
	{
		sh "${mavenHome}/bin/mvn clean package" // bat "mvn clean package" [for windows]
	}
	
	stage('Execute SonarQube Report')
	{
	    sh "${mavenHome}/bin/mvn sonar:sonar"
	}
	
	stage('Upload artifacts into Nexus')
	{
	    sh "${mavenHome}/bin/mvn deploy"
	}
	
	stage('Deploy application to Tomcat server')
		{
			sshagent(['tomcatPemFileID']) {
			sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@15.207.19.133:/opt/software/apache-tomcat-9.0.45/webapps"
		}
	
	stage('send Email Notification')
	{
		emailext body: '''Build:${BRANCH_NAME}

		Regards,
		YoungStone Tech''', subject: 'Build Over', to: 'alsamad0786@gmail.com'
	}
	}
}
