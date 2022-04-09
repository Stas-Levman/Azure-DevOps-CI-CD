# CI-CD with Azure DevOps

In this project I built a CI-CD pipeline for the weight tracker application and also a Terraform pipeline for the infrastructure, both in YAML format in Azure DevOps.<br>
The pipline uses the weight tracker web application in this repository, an Ansible playbook for the deployment in the Ansible repository
and Terraform for the infrastrcture in it's own repository.<br>
The pipeline was ran on a self-hosted agent in a non cloud environment (on prem).

1. Ansible repo - hlink
2. Terraform repo - hlink

### Notes and assumptions

- The main CI-CD pipeline assumes the infrastructure is already deployed, for both the stage environment and the prod environment.
- Both CI-CD and the Terraform pipelines demand a manual approval for the prod environment.
- Each of the pipeline repositories contain YAML templates to use in it's respective pipelines, for the .env file creation and to update Okta via API.
- The pipelines assume that azure has been logged in to via `az login` or credentials are avaiavlbe.
- The pipelines also assumes the host agent is ready for all it's tasks and dependencies (node 14.x, ansible, terraform).
- Both pipelines are triggered with commits to their respective repositories, except a few paths/files specified in the YAML's.
- Both "stage" and "prod" environments are configured in the "Environments" tab of azure DevOps.
- All the 


#### CI-CD workflow

1. In the CI stage, the latest commit of the repository is cloned, built with `npm install`, zipped to the artifact staging directory, and finally packaged via universal packages, certain files are removed beforehand as they are not part of the artifacts.<br>
2. in both the CD stages, the zipped artifacts are being downloaded to the working directory, and also the ansible repository.
3. For both stages ansible runs a playbook that copies and unrachives the 






