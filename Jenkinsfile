#!/bin/bash +x

import groovy.json.JsonOutput

def slackURL = 'https://hooks.slack.com/services/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'
def jobName = ((env.JOB_NAME).split('/'))[2]
def buildNo = env.BUILD_NUMBER

node('remote-docker-ssh') {    
    stage('Test Slack'){
        sendToSlack(" ",[
		[
			title: "Project name: ${jobName}",
			color: "warning",
			text: "Start a build #${buildNo}"
		]
        ], slackURL)
    }     
}
def sendToSlack(text, attachments, slackURL) {
    def payload = JsonOutput.toJson([
		text: text,
		attachments: attachments
	])
    sh '#!/bin/sh -e\n' + "curl -X POST --data-urlencode \'payload=${payload}\' ${slackURL}"
}