node
{
	properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])

	def  mvnHome = tool name: "maven3.6.3"     //global variable

	stage("CheckOutCode")
	{
	git branch: 'development', credentialsId: 'bcf8b890-b6a4-4811-abaf-a4b3f0a91504', url: 'https://github.com/ups-git/maven-web-application.git'
	}

	stage("Build")
	{
	sh "${mvnHome}/bin/mvn clean package"
	}

	stage("ExecuteSonarQubeReport")
	{
	sh "${mvnHome}/bin/mvn sonar:sonar"	
	}

	stage("UploadArtifactIntoNexus")
	{
	sh "${mvnHome}/bin/mvn deploy"
	}

	stage("DeployApplicationIntoTomcat")
	{
		sshagent(['34a534bc-9062-4d07-bf96-1b4749aeb4c1']) 
		{
		    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@3.88.174.198:/opt/apache-tomcat-9.0.31/webapps/"
		}
	}
	
	stage("SendEmailNotification")
	{
	emailext body: '''Build is over..

	Regards..
	DevOps Engineer
	Gopinath''', subject: 'Build is over', to: 'gnath.qa@gmail.com'
	}
}
