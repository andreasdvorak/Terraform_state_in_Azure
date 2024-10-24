# Store the Terraform state in Azure
Desription how to store the Terraform state in Azure

# Preparations
## Azure CLI
The following did not work for me.

lsb_release -cs shows virginia but there is no virginia on the web site

https://packages.microsoft.com/repos/azure-cli/dists/

https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt


This did work

    curl -L https://aka.ms/InstallAzureCli | bash

## az extension
I don´t know why but without the aks-preview extention I get an error setting up the AKS cluster

    az extension add -n aks-preview
    az extension list
    az version

## az login
To run the terraform apply you first need to login at Azure

    az login

## Get Azure Regions
    az account list-locations -o table

## List Subscriptions
    az account list

# Create Azure storage
To store the Terraform state file you need three things

- resource group
- storage account
- storage container

## Create Azure Storage account
    ./azure_storage.sh

## Configure terraform backend
Get the storage access key

    export RESOURCE_GROUP_NAME=
    export STORAGE_ACCOUNT_NAME=
    ACCOUNT_KEY=$(az storage account keys list --resource-group $RESOURCE_GROUP_NAME --account-name $STORAGE_ACCOUNT_NAME --query '[0].value' -o tsv)
    export ARM_ACCESS_KEY=$ACCOUNT_KEY

# Code
## terraform.tfvars
put in here all secret values

This file should not be put in git, because it contains secrets.

Check .gitignore

## client access values
To access Azure with Terraform you need a client_id client_secret

Create a user e.g. terraform in https://entra.microsoft.com

In the overview you get the client id

To to certifcates & secrets and create a secret client key. Copy the value.

## locals.tf
put in here all values, but no secrets

## provider.tf
Insert the real name here. You can find it in the script output or in the Azure portal.

storage_account_name = "<storage_account_name>"

# Terraform commands
## initialze Terraform
    terraform init [-upgrade]

## Terraform Validate
    terraform validate

## create a Terraform execution plan
    terraform plan -var-file="terraform.tfvars"

## execute Terraform
    terraform apply -var-file="terraform.tfvars"

## delete all remote objets
    terraform destroy
