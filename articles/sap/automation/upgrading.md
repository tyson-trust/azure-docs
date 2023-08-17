---
title: Upgrading the SAP on Azure Deployment Automation Framework
description: Overview of how to update the SAP on Azure Deployment Automation Framework.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 05/19/2023
ms.topic: how-to
ms.service: sap-on-azure
ms.subservice: sap-automation
ms.custom: devx-track-azurecli
---

# Upgrading the SAP on Azure Deployment Automation Framework

The SAP on Azure Deployment Automation Framework is updated regularly. This article describes how to update the framework.

## Prerequisites

Before upgrading the framework, make sure that you have backed up the following files:

- The remote state files from the 'tfstate' Storage account in the SAP Library.

## Upgrading the pipelines.

You can upgrade the pipeline definitions by running the 'Upgrade Pipelines' pipeline.

### Creating the 'Upgrade pipelines' pipeline manually

If you don't have the 'Upgrade Pipelines' pipeline, you can create it manually by following these steps:

Go to the pipelines folder in your repository and create the pipeline definition by choosing file from the New menu, name the file '21-update-pipelines.yml' and paste the following content into the file.

```yaml
---
  # /*---------------------------------------------------------------------------8
  # |                                                                            |
  # |               This pipeline updates the ADO repository                     |
  # |                                                                            |
  # +------------------------------------4--------------------------------------*/

  name:                                  Update Azure DevOps repository from GitHub $(branch) branch

  parameters:
    - name:                              repository
      displayName:                       Source repository
      type:                              string
      default:                           https://github.com/Azure/sap-automation-bootstrap.git

    - name:                              branch
      displayName:                       Source branch to update from
      type:                              string
      default:                           main

    - name:                              force
      displayName:                       Force the update
      type:                              boolean
      default:                           false

  trigger:                               none

  pool:
    vmImage:                             ubuntu-latest

  variables:
    - name:                              repository
      value:                             ${{ parameters.repository }}
    - name:                              branch
      value:                             ${{ parameters.branch }}
    - name:                              force
      value:                             ${{ parameters.force }}
    - name:                              log
      value:                             logfile_$(Build.BuildId)

  stages:
    - stage:                             Update_DEVOPS_repository
      displayName:                       Update DevOps pipelines
      jobs:
        - job:                           Update_DEVOPS_repository
          displayName:                   Update DevOps pipelines
          steps:
            - checkout:                  self
              persistCredentials:        true
            - bash: |
                #!/bin/bash
                green="\e[1;32m" ; reset="\e[0m" ; boldred="\e[1;31m"

                git config --global user.email "$(Build.RequestedForEmail)"
                git config --global user.name "$(Build.RequestedFor)"
                git config --global pull.ff false
                git config --global pull.rebase false

                git remote add remote-repo $(repository) >> /tmp/$(log) 2>&1

                git fetch --all --tags >> /tmp/$(log) 2>&1
                git checkout --quiet origin/main

                git checkout --quiet remote-repo/main ./pipelines/01-deploy-control-plane.yml
                git checkout --quiet remote-repo/main ./pipelines/02-sap-workload-zone.yml
                git checkout --quiet remote-repo/main ./pipelines/03-sap-system-deployment.yml
                git checkout --quiet remote-repo/main ./pipelines/04-sap-software-download.yml
                git checkout --quiet remote-repo/main ./pipelines/05-DB-and-SAP-installation.yml
                git checkout --quiet remote-repo/main ./pipelines/10-remover-terraform.yml
                git checkout --quiet remote-repo/main ./pipelines/11-remover-arm-fallback.yml
                git checkout --quiet remote-repo/main ./pipelines/12-remove-control-plane.yml
                git checkout --quiet remote-repo/main ./pipelines/20-update-repositories.yml
                git checkout --quiet remote-repo/main ./pipelines/22-sample-deployer-configuration.yml
                git checkout --quiet remote-repo/main ./pipelines/21-update-pipelines.yml
                return_code=$?

                if [[ "$(force)" == "True" ]]; then
                  echo "running git push to ADO with force option"
                  if ! git -c http.extraheader="AUTHORIZATION: bearer $(System.AccessToken)" push --force origin HEAD:$(branch) >> /tmp/$(log) 2>&1
                  then
                     echo -e "$red--- Failed to push ---$reset"
                     exit 1
                  fi
                else
                  git commit -m "Update ADO repository from GitHub $(branch) branch" -a
                  echo "running git push to ADO"
                  if ! git -c http.extraheader="AUTHORIZATION: bearer $(System.AccessToken)" push origin HEAD:$(branch) >> /tmp/$(log) 2>&1
                  then
                     echo -e "$red--- Failed to push ---$reset"
                     exit 1
                  fi

                fi
                # If Pull already failed then keep that error code
                if [ 0 != $return_code ]; then
                  return_code=$?
                fi

                exit $return_code

              displayName:               Update DevOps pipelines
              env:
                SYSTEM_ACCESSTOKEN:      $(System.AccessToken)
              failOnStderr:              true
...


```

Commit the changes to save the file to the repository, and then create the pipeline in Azure DevOps.

Create the 'Upgrade Pipelines' pipeline by choosing _New Pipeline_ from the Pipelines section, select 'Azure Repos Git' as the source for your code. Configure your Pipeline to use an existing Azure Pipelines YAML File. Specify the pipeline with the following settings:

| Setting | Value                                        |
| ------- | -------------------------------------------- |
| Branch  | main                                         |
| Path    | `deploy/pipelines/21-update-pipelines.yml`   |
| Name    | Upgrade pipelines                            |

Save the Pipeline, to see the Save option select the chevron next to the Run button. Navigate to the Pipelines section and select the pipeline. Rename the pipeline to 'Upgrade pipelines' by choosing 'Rename/Move' from the three-dot menu on the right.

Run the pipeline to upgrade all pipeline definitions.

## Upgrading the control plane.

The control plane is the first component to be upgraded. You can upgrade the control plane by rerunning the 'Deploy Control Plane' pipeline or by rerunning the deploy_controlplane.sh script.

### Upgrading to version 3.8.1

Run the following commands before performing the upgrade of the Control plane.

```azurecli

az login

az account set --subscription <subscription id>    

az vm run-command invoke -g <DeployerResourceGroup> -n <deployerVMName> --command-id RunShellScript --scripts "sudo rm /etc/profile.d/deploy_server.sh"
az vm extension delete -g <DeployerResourceGroup> -n <deployerVMName> -n configure_deployer

```

The script removes the old deployer configuration and allows the new configuration to be applied.

### Private DNS considerations

If using Private DNS Zones from the control plane, run the following command before performing the upgrade.

```azurecli

az network private-dns zone create --name privatelink.vaultcore.azure.net --resource-group <SAPLibraryResourceGroup>

```

### Agent sign-in

You can also configure the DevOps agent to perform the sign-in to Azure using the service principal by adding the following variable to the variable group used by the control plane pipeline, typically 'SDAF-MGMT'.

| Name             | Value                                        |
| ---------------- | -------------------------------------------- |
| Logon_Using_SPN  | true                                         |


## Upgrading the workload zone.

The workload zone is the second component to be upgraded. You can upgrade the control plane by rerunning the 'SAP Workload Zone deployment' pipeline or by rerunning the install_workloadzone.sh script.

### Upgrading to version 3.8.1


Prepare for the upgrade by first retrieving the private DNS zone resource ID and the key vault private endpoint name by running the following commands.

```azurecli

az network private-dns zone show  --name privatelink.vaultcore.azure.net --resource-group <SAPLibraryResourceGroup> --query id --output tsv

az network private-endpoint list  --resource-group <WorkloadZoneResourceGroup> --query "[?contains(name,'keyvault')].{Name:name} | [0] | Name" --output tsv

```


If you're using private endpoints, run the following command before performing the upgrade to update the DNS settings for the private endpoint. Replace the 'privateDNSzoneResourceId' and 'keyvaultEndpointName' placeholders with the values retrieved in the previous step.

```azurecli

az network private-endpoint dns-zone-group create --resource-group <WorkloadZoneResourceGroup> --endpoint-name <keyvaultEndpointName> --name privatelink.vaultcore.azure.net --private-dns-zone <privateDNSzoneResourceId>  --zone-name privatelink.vaultcore.azure.net

```

### Agent sign-in for workload zone and system deployments


You can also configure the DevOps agent to perform the sign in to Azure using the service principal by adding the following variable to the variable group used by the control plane pipeline, typically 'SDAF-DEV'.

| Name             | Value                                        |
| ---------------- | -------------------------------------------- |
| Logon_Using_SPN  | true                                         |



## Next step

> [!div class="nextstepaction"]
> [Configure the Control plane](configure-control-plane.md)
