#!/bin/bash +x

import groovy.json.JsonOutput

def slackURL = 'https://hooks.slack.com/services/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'

def sendToSlack(text, attachments, slackURL) {
    def payload = JsonOutput.toJson([
		text: text,
		attachments: attachments
	])
    sh '#!/bin/sh -e\n' + "curl -X POST --data-urlencode \'payload=${payload}\' ${slackURL}"
}

def notifyLINE(status) {
  def token = "AKINBMs1efYjcK3W2AtFtw1VEDpi4Xp5ArcQf52OSVS"
  def jobName = env.JOB_NAME +' '+env.BRANCH_NAME
  def buildNumber = env.BUILD_NUMBER

  def url = 'https://notify-api.line.me/api/notify'
  def message = "${jobName} Build #${buildNumber} ${status} \r\n"
  sh "curl ${url} -H 'Authorization: Bearer ${token}' -F 'message=${message}'"
}

pipeline {
  environment {
    registry = "kowoatz/test-jenkins-pipeline"
    registryCredential = 'kowoatz'
    dockerImage = ''
  }

  agent any  
  stages {
    stage('Cloning git') {
      steps {
        git  'https://github.com/AnuchitKumhomkul/testjenkins.git'
      }
    }

    stage('Building image') {
      steps {
        script {
          img = docker.build registry
        }
      }
    }

    stage('Deploy Image') {
      steps {
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
              img.push("${BUILD_NUMBER}")
              img.push("latest")
          }
        }
      }
    }

	stage('Notify') {
      steps {
        script {
		  def jobName = env.JOB_NAME +' '+env.BRANCH_NAME
  		  def buildNumber = env.BUILD_NUMBER
          if (currentBuild.result == 'FAILURE') {
			sendToSlack(" ",[
			[
				title: "Project name: ${jobName}",
				color: "warning",
				text: "Start a build #${buildNo}"
			]
        	], slackURL)
		  }else {
			sendToSlack(" ",[
			[
				title: "Project name: ${jobName}",
				color: "warning",
				text: "Start a build #${buildNo}"
			]
        	], slackURL) 
		  }
        }
      }
    }

  }
  post{
    success{
      notifyLINE("Succeed")
    }
    failure{
      notifyLINE("Failed")
    }
  }
}