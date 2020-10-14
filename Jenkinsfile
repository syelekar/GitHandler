pipeline
{
    agent { label 'master' }
    parameters
    {
        choice(name: "RELEASE_ENVIRONMENT", choices: ["QA","Stage","Prod"], description: "Select the release environment")
    }
    stages
    {
        stage('DeployQA')
        {
            when
            {
                expression{params.RELEASE_ENVIRONMENT == "QA"}
            }
            steps
            {      
                sh '''
				  account_id=$(aws ssm get-parameter --name travel-qa-id --with-decryption --region us-east-1 | jq -r .Parameter.Value)
                  role="arn:aws:iam::${account_id}:role/travel-qa-eks-deploy-role"
                  aws sts assume-role --role-arn $role --role-session-name TemporarySessionKeys --output json > assume-role-output.json
                  export AWS_ACCESS_KEY_ID=$(jq -r '.Credentials.AccessKeyId' assume-role-output.json)
                  export AWS_SECRET_ACCESS_KEY=$(jq -r '.Credentials.SecretAccessKey' assume-role-output.json)
                  export AWS_SESSION_TOKEN=$(jq -r '.Credentials.SessionToken' assume-role-output.json)
                  aws s3 cp index.html s3://sdlc-toolchain-qa/demo/qa/index.html
				  unset AWS_ACCESS_KEY_ID
                  unset AWS_SECRET_ACCESS_KEY
                  unset AWS_SESSION_TOKEN
                    '''
            }
        }
		stage('DeployStage')
        {
            when
            {
                expression{params.RELEASE_ENVIRONMENT == "Stage"}
            }
            steps
            {      
                sh '''
				  account_id=$(aws ssm get-parameter --name travel-qa-id --with-decryption --region us-east-1 | jq -r .Parameter.Value)
                  role="arn:aws:iam::${account_id}:role/travel-qa-eks-deploy-role"
                  aws sts assume-role --role-arn $role --role-session-name TemporarySessionKeys --output json > assume-role-output.json
                  export AWS_ACCESS_KEY_ID=$(jq -r '.Credentials.AccessKeyId' assume-role-output.json)
                  export AWS_SECRET_ACCESS_KEY=$(jq -r '.Credentials.SecretAccessKey' assume-role-output.json)
                  export AWS_SESSION_TOKEN=$(jq -r '.Credentials.SessionToken' assume-role-output.json)
                  aws s3 cp index.html s3://sdlc-toolchain-qa/demo/stage/index.html
				  unset AWS_ACCESS_KEY_ID
                  unset AWS_SECRET_ACCESS_KEY
                  unset AWS_SESSION_TOKEN
                    '''
            }
        }
		stage('DeployProd')
        {
            when
            {
                expression{params.RELEASE_ENVIRONMENT == "Prod"}
            }
            steps
            {      
                sh '''
		  account_id=$(aws ssm get-parameter --name travel-qa-id --with-decryption --region us-east-1 | jq -r .Parameter.Value)
                  role="arn:aws:iam::${account_id}:role/travel-qa-eks-deploy-role"
                  aws sts assume-role --role-arn $role --role-session-name TemporarySessionKeys --output json > assume-role-output.json
                  export AWS_ACCESS_KEY_ID=$(jq -r '.Credentials.AccessKeyId' assume-role-output.json)
                  export AWS_SECRET_ACCESS_KEY=$(jq -r '.Credentials.SecretAccessKey' assume-role-output.json)
                  export AWS_SESSION_TOKEN=$(jq -r '.Credentials.SessionToken' assume-role-output.json)
                  aws s3 cp index.html s3://sdlc-toolchain-qa/demo/prod/index.html
		  unset AWS_ACCESS_KEY_ID
                  unset AWS_SECRET_ACCESS_KEY
                  unset AWS_SESSION_TOKEN
                    '''
            }
        }
    }   
}
