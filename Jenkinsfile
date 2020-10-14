pipeline
{
    agent { label 'master' }
    parameters
    {
        choice(name: "ACTION", choices: ["Build","deploy"], description: "Select the action")
		choice(name: "DEPLOY_ENVIRONMENT", choices: ["QA","Stage","Prod"], description: "Select the release environment")
		string(name: "JIRA_CARD", defaultValue: 'KR-9272', description: 'Select the release card')
    }
    stages
    {
		stage('Deploy')
        {
            when
            {
                expression{params.ACTION.toLowerCase() == "deploy"}
            }
            steps
            {    
				verifyJiraCard("${params.JIRA_CARD}", "k8s-demo-app", "${this.params.DEPLOY_ENVIRONMENT}")
				deploy()
				updateJiraCard("${params.JIRA_CARD}", "k8s-demo-app", "${this.params.DEPLOY_ENVIRONMENT}")
			}
        }
		stage('Build')
        {
			when
            {
                expression{params.ACTION.toLowerCase() == "b"}
            }
            steps
            {  
				sh '''
				echo " Build completed succesfully"
				'''
			}
		}
	}		
}

def deploy() {	
	s3pathToDeploy = 'abc'
	if (this.params.DEPLOY_ENVIRONMENT == "qa") {
	s3pathToDeploy = "s3://sdlc-toolchain-qa/demo/qa/index.html"
	}
	if (this.params.DEPLOY_ENVIRONMENT == "stage") {
	s3pathToDeploy = "s3://sdlc-toolchain-qa/demo/stage/index.html"
	}
	if (this.params.DEPLOY_ENVIRONMENT == "prod") {
	s3pathToDeploy = "s3://sdlc-toolchain-qa/demo/prod/index.html"
	}
	
	sh '''
	account_id=$(aws ssm get-parameter --name travel-qa-id --with-decryption --region us-east-1 | jq -r .Parameter.Value)
    role="arn:aws:iam::${account_id}:role/travel-qa-eks-deploy-role"
    aws sts assume-role --role-arn $role --role-session-name TemporarySessionKeys --output json > assume-role-output.json
    export AWS_ACCESS_KEY_ID=$(jq -r '.Credentials.AccessKeyId' assume-role-output.json)
    export AWS_SECRET_ACCESS_KEY=$(jq -r '.Credentials.SecretAccessKey' assume-role-output.json)
    export AWS_SESSION_TOKEN=$(jq -r '.Credentials.SessionToken' assume-role-output.json)
    aws s3 cp index.html ${s3pathToDeploy}
				  
	unset AWS_ACCESS_KEY_ID
    unset AWS_SECRET_ACCESS_KEY
    unset AWS_SESSION_TOKEN
    '''
}

def verifyJiraCard(String jira_card, String repo_name, String release_environment) {
  build job: '/Process Automation/Process-GateKeeper-Multiple-Cards-Verify', parameters: [
    string(name: 'JiraCards', value: jira_card),
    string(name: 'RepoName', value: repo_name),
    string(name: 'ReleaseEnv', value: release_environment)
  ]
}

def updateJiraCard(String jira_card, String repo_name, String release_environment) {
  build job: '/Process Automation/Process-GateKeeper-Multiple-Cards-Update', parameters: [
    string(name: 'JiraCards', value: jira_card),
    string(name: 'RepoName', value: repo_name),
    string(name: 'ReleaseEnv', value: release_environment)
  ]
}
