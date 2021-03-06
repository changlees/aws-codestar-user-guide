# Troubleshooting AWS CodeStar<a name="troubleshooting"></a>

The following information might help you troubleshoot common issues in AWS CodeStar\.

**Topics**
+ [Project creation failure: A project was not created](#troubleshooting-pc1)
+ [Project creation: I see an error when I try to edit Amazon EC2 configuration when creating a project](#troubleshooting-pc2)
+ [Project deletion: An AWS CodeStar project was deleted, but resources still exist](#troubleshooting-pd1)
+ [Team management failure: An IAM user could not be added to a team in an AWS CodeStar project](#troubleshooting-team1)
+ [Access failure: A federated user cannot access an AWS CodeStar project](#troubleshooting-federated1)
+ [Access failure: A federated user cannot access or create an AWS Cloud9 environment](#troubleshooting-federated2)
+ [Access failure: A federated user can create an AWS CodeStar project, but cannot view project resources](#troubleshooting-federated3)
+ [Service role issue: The service role could not be created](#troubleshooting-sr1)
+ [Service role issue: The service role is not valid or missing](#troubleshooting-sr2)
+ [Project role issue: AWS Elastic Beanstalk health status checks fail for instances in an AWS CodeStar project](#troubleshooting-eb-roles)
+ [Project role issue: A project role is not valid or missing](#troubleshooting-pp1)
+ [Project extensions: Can't connect to JIRA](#troubleshooting-jira)
+ [GitHub: Can't access a repository's commit history, issues, or code](#troubleshooting-github-access)

## Project creation failure: A project was not created<a name="troubleshooting-pc1"></a>

**Problem:** When you try to create a project, you see a message that the creation failed\.

**Possible fixes:** The most common reasons for failure are:
+ A project with that ID already exists in your AWS account, possibly in a different region\.
+ The IAM user you used to sign in to the AWS Management Console does not have the permissions required to create a project\.
+ The AWS CodeStar service role is missing one or more required permissions\.
+ You have reached the maximum limit for one or more resources for a project \(such as the limit on customer managed policies in IAM, Amazon S3 buckets, or pipelines in AWS CodePipeline\)\.

Before you create a project, verify that you have the **AWSCodeStarFullAccess** policy applied to your IAM user\. For more information, see [AWS CodeStar Access Permissions Reference](access-permissions.md)\.

When you create a project, make sure that the ID is unique and meets the AWS CodeStar requirements\. Be sure you selected the **AWS CodeStar would like permission to administer AWS resources on your behalf** check box\. 

To troubleshoot other issues, open the AWS CloudFormation console, choose the stack for the project you tried to create, and choose the **Events** tab\. There might be more than one stack for a project\. The stack names will start with `awscodestar-`, followed by the project ID\. Stacks might be under the **Deleted** filter view\. Review any failure messages in the stack events and correct the issue listed as the cause of those failures\.

## Project creation: I see an error when I try to edit Amazon EC2 configuration when creating a project<a name="troubleshooting-pc2"></a>

**Problem:** When you edit the Amazon EC2 configuration options during project creation, you see an error message or greyed\-out option, and cannot continue with project creation\.

**Possible fixes:** The most common reasons for an error message are:
+ The VPC in the AWS CodeStar project template \(either the default VPC, or the one used when the Amazon EC2 configuration was edited\) has dedicated instance tenancy, and the instance type is not supported for dedicated instances\. Either choose a different instance type or a different Amazon VPC\.
+ Your AWS account has no Amazon VPCs\. You might have deleted the default VPC, and not created any others\. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/), choose **Your VPCs**, and make sure that you have at least one VPC configured\. If not, create one\. For more information, see [Amazon Virtual Private Cloud Overview](http://docs.aws.amazon.com/AmazonVPC/latest/GettingStartedGuide/ExerciseOverview.html) in the *Amazon VPC Getting Started Guide*\.
+ The Amazon VPC does not have any subnets\. Choose a different VPC, or create a subnet for that VPC\. For more information, see [VPC and Subnet Basics](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Subnets.html#vpc-subnet-basics)\.

## Project deletion: An AWS CodeStar project was deleted, but resources still exist<a name="troubleshooting-pd1"></a>

**Problem:** An AWS CodeStar project was deleted, but resources created for that project still exist\. By default, AWS CodeStar deletes project resources when the project is deleted\. Some resources, such as Amazon S3 buckets, are retained even if the user selects the **Delete associated resources along with AWS CodeStar project** check box, as the buckets might contain data\.

**Possible fixes:** Open the [AWS CloudFormation console](https://console.aws.amazon.com//cloudformation) and find one or more of the AWS CloudFormation stacks used to create the project\. The stack names will start with `awscodestar-`, followed by the project ID\. The stacks might be under the **Deleted** filter view\. Review the events associated with the stack to discover the resources created for the project\. Open the console for each of those resources in the region where you created the AWS CodeStar project, and then manually delete the resources for that deleted project\. 

Project resources that might remain include:
+ One or more project buckets in Amazon S3\. Unlike other project resources, project buckets in Amazon S3 are not deleted when the **Delete associated AWS resources along with AWS CodeStar project** check box is selected\.

  Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.
+ A source repository for your project in AWS CodeCommit\.

  Open the AWS CodeCommit console at [https://console\.aws\.amazon\.com/codecommit/](https://console.aws.amazon.com/codecommit/)\.
+ A pipeline for your project in AWS CodePipeline\.

  Open the AWS CodePipeline console at [https://console\.aws\.amazon\.com/codepipeline/](https://console.aws.amazon.com/codepipeline/)\.
+ An application and associated deployment groups in AWS CodeDeploy\.

  Open the AWS CodeDeploy console at [https://console\.aws\.amazon\.com/codedeploy/](https://console.aws.amazon.com/codedeploy/)\.
+ An application and associated environments in AWS Elastic Beanstalk\.

  Open the Elastic Beanstalk console at [https://console\.aws\.amazon\.com/elasticbeanstalk/](https://console.aws.amazon.com/elasticbeanstalk/)\.
+ A function in AWS Lambda\.

  Open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.
+ One or more APIs in API Gateway\.

  Open the API Gateway console at [https://console\.aws\.amazon\.com/apigateway/](https://console.aws.amazon.com/apigateway/)\. 
+ One or more IAM policies or roles in IAM\. 

  Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.
+ An instance in Amazon Elastic Compute Cloud \(Amazon EC2\)\. 

  Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.
+ One or more development environments in AWS Cloud9\.

  To view, access, and manage development environments, open the AWS Cloud9 console at [https://console\.aws\.amazon\.com/cloud9/](https://console.aws.amazon.com/cloud9/)\.

If your project uses resources outside of AWS, for example a GitHub repository or issues in Atlassian JIRA, those resources are not deleted, even if the **Delete associated AWS resources along with CodeStar project** box is selected\.

## Team management failure: An IAM user could not be added to a team in an AWS CodeStar project<a name="troubleshooting-team1"></a>

**Problem:** When you try to add a user to a project, you see an error message saying that the addition failed\.

**Possible fixes:** The most common reason for this error is that the IAM user has reached the limit of managed policies that can be applied to a user in IAM\. You might also receive this error if you are not a member of the Owner role in the AWS CodeStar project where you tried to add the user, or if the IAM user does not exist or was deleted\. 

Make sure you are signed in as an IAM user who is an owner in that AWS CodeStar project\. For more information, see [Add Team Members to an AWS CodeStar Project ](how-to-add-team-member.md)\.

To troubleshoot other issues, open the IAM console, choose the user you tried to add, and check how many managed policies are applied to that IAM user\. 

For more information, see [Limitations on IAM Entities and Objects](http://docs.aws.amazon.com/IAM/latest/UserGuide/reference_iam-limits.html)\. For limits that can be changed, see [AWS Service Limits](http://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html)\.

## Access failure: A federated user cannot access an AWS CodeStar project<a name="troubleshooting-federated1"></a>

**Problem:** A federated user is unable to see projects in the AWS CodeStar console\.

**Possible fixes:** If you are signed in as a federated user, make sure you have the appropriate managed policy attached to the role you assume to sign in\. For more information, see [Attach Your Project's AWS CodeStar Viewer/Contributor/Owner Managed Policy to the Federated User's Role](access-permissions.md#access-permissions-federated-attach-CodeStar)\.

Add federated users to your AWS Cloud9 environment by manually attaching policies\. See [Attach an AWS Cloud9 Managed Policy to the Federated User's Role](access-permissions.md#access-permissions-federated-attach-Cloud9)\.

## Access failure: A federated user cannot access or create an AWS Cloud9 environment<a name="troubleshooting-federated2"></a>

**Problem:** A federated user is unable to see or create an AWS Cloud9 environment in the AWS Cloud9 console\.

**Possible fixes:** If you are signed in as a federated user, make sure you have the appropriate managed policy attached to the federated user's role\.

You add federated users to your AWS Cloud9 environment by manually attaching policies to the federated user's role\. See [Attach an AWS Cloud9 Managed Policy to the Federated User's Role](access-permissions.md#access-permissions-federated-attach-Cloud9)\.

## Access failure: A federated user can create an AWS CodeStar project, but cannot view project resources<a name="troubleshooting-federated3"></a>

**Problem:** A federated user was able to create a project, but cannot view project resources, such as the project pipeline\.

**Possible fixes:** If you have attached the **AWSCodeStarFullAccess** managed policy, you have permissions to create a project in AWS CodeStar\. However, to access all project resources, you must attach the Owner managed policy\.

After AWS CodeStar creates the project resources, project permissions to all project resources are available in the Owner, Contributer, and Viewer managed policies\. You must manually attach the Owner policy to your role to access all of the resources\. See [Configure Permissions for Federated Users](setting-up.md#setting-up-create-federated-user)\.

## Service role issue: The service role could not be created<a name="troubleshooting-sr1"></a>

**Problem:** When you try to create a project in AWS CodeStar, you see a message prompting you to create the service role\. When you choose the option to create it, you see an error\.

**Possible fixes:** The most common reason for this error is that you are logged into AWS with an account that does not have sufficient permissions to create the service role\. To create the AWS CodeStar service role \(`aws-codestar-service-role`\), you must be logged in as an administrative user or with a root account\. Log out of the console, and log back in with an IAM user that has the `AdministratorAccess` managed policy applied\.

## Service role issue: The service role is not valid or missing<a name="troubleshooting-sr2"></a>

**Problem:** When you open the AWS CodeStar console, you see a message indicating the AWS CodeStar service role is missing or not valid\.

**Possible fixes:** The most common reason for this error is that an administrative user edited or deleted the service role \(`aws-codestar-service-role`\)\. If the service role was deleted, you will be prompted to create it\. You must be logged in as an administrative user or with a root account to create the role\. If the role was edited, it is no longer valid\. Log in to the IAM console as an administrative user, find the service role in the list of roles, and delete it\. Switch to the AWS CodeStar console and follow the on\-screen instructions to create the service role\.

## Project role issue: AWS Elastic Beanstalk health status checks fail for instances in an AWS CodeStar project<a name="troubleshooting-eb-roles"></a>

**Problem:** If you created an AWS CodeStar project that includes Elastic Beanstalk before September 22, 2017, Elastic Beanstalk health status checks might fail\. If you have not changed the Elastic Beanstalk configuration since you created the project, the health status check fails and reports a grey state\. Despite the health check failure, your application should still run as expected\. If you changed the Elastic Beanstalk configuration since you created the project, the health status check fails, and your application might not run correctly\.

**Fix:** One or more IAM roles are missing required IAM policy statements\. Add the missing policies to the affected roles in your AWS account\.

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

   \(If you cannot do this, see your AWS account administrator for assistance\.\)

1. In the navigation pane, choose **Roles**\.

1. In the list of roles, choose **CodeStarWorker\-*Project\-ID*\-EB**, where *Project\-ID* is the ID of one of the affected projects\. \(If you cannot easily find a role in the list, type some or all of the role's name in the **Search** box\.\)

1. On the **Permissions** tab, choose **Attach Policy**\.

1. In the list of policies, select **AWSElasticBeanstalkEnhancedHealth** and **AWSElasticBeanstalkService**\. \(If you cannot easily find a policy in the list, type some or all of the policy's name in the search box\.\)

1. Choose **Attach Policy**\.

1. Repeat steps 3 through 6 for each affected role that has a name following the pattern **CodeStarWorker\-*Project\-ID*\-EB**\.

## Project role issue: A project role is not valid or missing<a name="troubleshooting-pp1"></a>

**Problem:** When you try to add a user to a project, you see an error message saying that the addition failed because the policy for a project role is either missing or not valid\.

**Possible fixes:** The most common reason for this error is that one or more project policies was edited in or deleted from IAM\. Project policies are unique to AWS CodeStar projects, and cannot be recreated\. The project cannot be used\. Create a new project in AWS CodeStar, and migrate data to the new project\. Clone project code from the unusable project's repository, and push that code to the new project's repository\. Copy team wiki information from the old project to the new project\. Add users to the new project\. When you are sure you have migrated all data and settings, delete the unusable project\.

## Project extensions: Can't connect to JIRA<a name="troubleshooting-jira"></a>

**Problem:** When you use the **Atlassian JIRA** extension to try to connect an AWS CodeStar project to a JIRA instance, the following message displays: "The URL is not a valid JIRA URL\. Verify that the URL is correct\."

**Possible fixes:**
+ Make sure the JIRA URL is correct, and then try connecting again\.
+ Your self\-hosted JIRA instance may not be accessible from the public Internet\. Contact your network administrator to make sure your JIRA instance can be accessed from the public Internet, and then try connecting again\.

## GitHub: Can't access a repository's commit history, issues, or code<a name="troubleshooting-github-access"></a>

**Problem:** In the dashboard for a project that stores its code in GitHub, the **Commit history** and **GitHub Issues** tiles display a connection error, or choosing **Open in GitHub** or **Create issue** in these tiles displays an error\.

**Possible causes:**
+ The AWS CodeStar project may no longer have access to the GitHub repository\.
+ The repository may have been deleted or renamed in GitHub\.