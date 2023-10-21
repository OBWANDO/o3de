# Jenkins Runbook


## Getting Started

The Build SIG maintains the Jenkins pipeline and users in the [aws-ops](https://github.com/orgs/o3de/teams/aws-ops) group can approve changes to the infrastructure required to run it. A select group of maintainers within SIG-Build will have access to the AWS account hosting the underlying infrastructure. Contact the chair/co-chair of [#sig-build](https://discord.com/channels/805939474655346758/816043576034328636) for more info. 

The purpose of this runbook is to instruct Build SIG maintainers how to trigger a deployment of the O3DE Jenkins server. Deployments are automated by the pipeline detailed here: [O3DE Jenkins Pipeline Repo RFC](https://github.com/o3de/sig-build/blob/main/rfcs/rfc-bld-20220209-1-jenkins-pipeline-repo.md)

## Resources

| Jenkins Instance | Description | Git Repo/Branch |
| --- | --- | --- |
| https://jenkins.build.o3de.org/ | O3DE Prod Instance | o3de-jenkins-pipeline / branch: o3de-prod | 
| https://jenkins-sandbox.build.o3de.org/ | O3DE Sandbox Instance | o3de-jenkins-pipeline | branch: o3de-sandbox |

GitHub Repo URL: https://github.com/o3de/o3de-jenkins-pipeline 


## Testing

Follow the steps in [Jenkins Deployment Guide](https://github.com/o3de/sig-build/blob/main/AutomatedReview/JenkinsDeploymentGuide.md) to perform testing prior to submitting a pull request. 


## Prod Deployment

Proceed to these steps after the changes have been tested in the sandbox.

1. Check out the `o3de-prod` branch and get latest.
2. Create a new branch to stage the changes to deploy.
3. Merge in changes that were tested in the sandbox branch.
```
# Merge individual files with:
git checkout <branch-name> <file-name>
```
4. Commit and push files to the branch
5. Submit a pull request with `o3de-prod` as the target. Main will be selected by default, so make sure this is changed.
6. A maintainer on `sig-build` with the required access will review and merge in the pull request.
7. The change will be deployed through the pipeline.
8. Approve changes in the final approval step in CodePipeline. 

## Monitoring Deployments

Build SIG maintainers that have access to the AWS account can view the progress of the deployment in CodePipeline.

There are 3 main sections to the pipeline:

1.  Build: The CDK packages are generated and changes are deployed if required, including changes to the pipeline itself.
2.  Assets: A new docker image is created and uploaded where it will be deployed by ECS.
3.  Deploy: The target stack is updated to use the new docker image. This results in the deployment of a new ECS task. Traffic will be routed to the new task once it's online.

### Logs

Click on the **Details** link on each section to view the related logs. 

If a new task is failing to start, you can view the logs in the ECS service page. Click on the task ID then click on the Logs tab. The older task will not be stopped until the new one passes the health checks. 

## Troubleshooting Deployments

Go to [Jenkins Deployment Guide](https://github.com/o3de/sig-build/blob/main/AutomatedReview/JenkinsDeploymentGuide.md) to view solutions to common issues encountered during deployments. 

### Account Details

| Jenkins Instance | AWS Account | CodePipeline | EFS ID |
| --- | --- | --- | --- | 
| https://jenkins.build.o3de.org/ | 206706238973 | JenkinsPipelineStack-Pipeline9850B417-3P7BBQIUJKL0 | fs-03efa64a6afe6ef13 |
