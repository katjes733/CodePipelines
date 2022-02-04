# CodePipelines
Set of CloudFormation Templates to setup more advanced CodePipelines such as deployment testing of CloudFromation templates continuously as well as building various other things
This set of Cloudofmation templates sets up all necessary prerequisites and the actual CodePiplines

# Usage
1. In an arbitrary region (e.g. us-east-1), deploy Template 000.
   - Name the Stack: e.g. Account-CD-Prerequisites
   - Review
     - Acknowledge that AWS CloudFormation might create IAM recources with custom names and Create stack
1. In an arbitrary region (e.g. us-east-1), create a StackSet using Template 001.
   - Specify StackSet Details:
     - Name the StackSet: e.g. Regional-CD-Prerequisites
     - Specify Parameter ResourcePrefix or leave it empty: e.g. m-pre-
     - Adjust Parameter ArtifactBucketName as necessary
   - Configure StackSet options
     - IAM admin role ARN: Select AWSCloudFormationStackSetAdministrationRole
     - IAM execution role name: Leave as AWSCloudFormationStackSetExecutionRole
   - Set deployment options
     - Select Deploys new stacks
     - Select Deploy stacks in accounts and specify the current account number: e.g., 111122223333
     - Specify the desired target region(s) from the following list (others are currently not supported):
       - US East (N.Virginia) - us-east-1
       - US West (Oregon) - us-west-2
       - EU (Frankfurt) - eu-central-1
       - EU (Ireland) - eu-west-1
       - Asia Pacific (Tokyo) - ap-northeast-1
       - Asia Pacific (Seoul) - ap-northeast-2
       - Asia Pacific (Singapore) - ap-southeast-1
       - Asia Pacific (Sydney) - ap-southeast-2
       - EU (London) - eu-west-2
       - Asia Pacific (Mumbai) - ap-south-1
     - Select Region Concurrency to Parallel for faster deployment, otherwise leave everything else at default
   - Review
     - Acknowledge that AWS CloudFormation might create IAM recources with custom names and Submit
   - Wait for the StackSet to be created and the individual stacks in each region to be created.
1. In the desired region to host the CodePipelines, deploy Template 002
   - Name the Stack: e.g. Local-CD-Prerequisites
   - Specify your WebHookUrl, which represents the Teams channel webhook URL used to display notifications. While the parameter itself can be left empty, some functionality will not function if no functioning URL is provided.
   - All other Parameters should be left untouched
   - Review
     - Acknowledge that AWS CloudFormation might create IAM recources with custom names and Create stack
1. In the desired region to host the CodePipelines, deploy Template 003 CloudFormation
   - Specify StackSet Details:
     - Stack Name: e.g. Local-CD-CFN (no more than 17 characters)
     - Parameters:
       - TestStackName: e.g. test
       - TestStackConfig: Adjust as necessary
       - TemplateFileName: Adjust as necessary
       - GlobalArtifactBucketNamePrefix: Adjust as necessary
       - GlobalArtifactS3Prefix: Adjust as necessary
       - DeploymentRegions: Specify into what regions you would like the test template to be deployed.
       - For Continuos Deployment based on execution rate with templates in Artifact bucket
         - SourceObjectKey: arbitrary filename, but that file has to exist in the Pipeline source bucket
         - ExecutionRate: specify a rate in hours. Should be at least the time the test template takes to deploy and undeploy (e.g. if deployment takes 45m and undeployment takes 20m, set to 2)
         - RepositoryName: leave empty
         - BranchName: arbitrary value



