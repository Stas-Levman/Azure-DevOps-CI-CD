# CI-CD with Azure DevOps

In this project I built a CI-CD pipeline for the weight tracker application and also a Terraform pipeline for the infrastructure, both in YAML format in Azure DevOps. Authorization is done via Okta.<br>
The pipline uses the weight tracker web application in this repository, an Ansible playbook for the deployment in the Ansible repository
and Terraform for the infrastrcture in it's own repository.<br>
The pipeline was ran on a self-hosted agent in a non cloud environment (on prem).

1. <a href="https://github.com/sincros121/Weight-tracker-ansible-pipeline" title="">Terraform repo</a>
2. <a href="https://github.com/sincros121/Weight-tracker-terraform-pipeline" title="">Ansible repo</a>

### Notes and assumptions

- The main CI-CD pipeline assumes the infrastructure is already deployed, for both the stage environment and the prod environment.
- Both CI-CD and the Terraform pipelines demand a manual approval for the prod environments
- Each of the pipeline repositories contain YAML templates to use in it's respective pipelines, for the .env file creation and to update Okta via API.
- The pipelines assume that azure has been logged in to via `az login` or credentials are avaiavlbe.
- The pipelines also assumes the host agent is ready for all it's tasks and dependencies (node 14.x, ansible, terraform).
- Both pipelines are triggered with commits to their respective repositories, except a few paths/files specified in the YAML's.
- CI-CD is triggered by the "stage" environment completion in the Terraform pipeline.
- Both "stage" and "prod" environments are configured in the "Environments" tab of azure DevOps.
- The library has the neccesery variable groups defined and contains both .tfvars files for each of the environments for Terraform to use.
- The created .env file is deleted from the working directory upon completion of the Ansible task for security reason.


#### CI-CD workflow

1. In the CI stage, the latest commit of the repository is cloned, built with `npm install`, zipped to the artifact staging directory, and finally packaged via universal packages, certain files are removed beforehand as they are not part of the artifacts.<br>
2. in both the CD stages, the zipped artifacts are being downloaded to the working directory, the Ansible repository cloned, and the .env file is created.
3. For both stages ansible runs a playbook that copies and unrachives the artifacts into the target VMs together with the .env file, and configures the application.
4. the .env file is deleted from the working directory.

#### Terraform workflow

1. The first stage makes sure terraform is prepered to build the infrastructure, it clones the terraform repository, runs `terraform init` to initialize Terraform and checks if both the stage and prod workspaces exist.
2. In the 2nd and 3rd stages, terrform downloads the secure .tfvars files for each of the environments, selects the correct workspace via `terraform workspace select` and deploys the infrastructure via command: `terraform apply -var-file "$(prod_tfvars.secureFilePath)" -auto-approve`.
3. The profiles of both the applications in Okta are updated with the new IP's assigned by Azure via an API script.


