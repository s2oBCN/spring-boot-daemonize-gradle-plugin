#!/usr/bin/env groovy

@Library("pipeline-init@master")
import roche.devops.tools.ArtifactoryTools

node('dockerserver')
{
	def currentBranch

	stage('Checkout')
	{
		checkout scm
		sh 'git clean -fdx'
		currentBranch = env.BRANCH_NAME
	}
	
	// Todo create a Roche docker image		
	docker.image('dgroup/java8-gradle').inside
    {
        def buildInfo
        def server = Artifactory.newServer url: 'http://repository.kiosk.roche.com/', credentialsId: 'artifactoryCredentialsID'
        def rtGradle = Artifactory.newGradleBuild()
        rtGradle.resolver server: server, repo: 'maven'
        rtGradle.deployer server: server, repo: 'maven-snapshot-local'

        stage('Compile')
        {
             buildInfo = rtGradle.run rootDir: ".", buildFile: 'build.gradle', tasks: 'clean artifactoryPublish'
        }

		stage("Publish"){
            server.publishBuildInfo buildInfo
        }
    }
}