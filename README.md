# Azure Purview Demo Environment
This repository includes a template (i.e. Bicep + PowerShell) that can be used to automate the deployment of an Azure Purview demo environment.

## Prerequisites

* An active [Azure subscription](https://azure.microsoft.com/en-us/free/).
* A resource group to which you have [Owner](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles) permissions. 
* [Register an application with Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#register-an-application-with-azure-ad-and-create-a-service-principal) and [create a new application secret](https://docs.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal#option-2-create-a-new-application-secret).

## Usage
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Ftayganr%2Fpurviewdemo%2Fmain%2Fbicep%2Fazuredeploy.json)

<!-- 1. Download the following files to your local machine:
    * [azuredeploy.bicep](https://raw.githubusercontent.com/tayganr/purviewdemo/main/bicep/azuredeploy.bicep)
    * [azuredeploy.parameters.json](https://raw.githubusercontent.com/tayganr/purviewdemo/main/bicep/azuredeploy.parameters.json)
    * [purview.ps1](https://raw.githubusercontent.com/tayganr/purviewdemo/main/bicep/purview.ps1)
2. Update **azuredeploy.parameters.json** file with your values.
3. Execute the following command:

`az deployment group create -g YOUR_RESOURCE_GROUP -f azuredeploy.bicep -p parameters.json ` -->

## Resources

* Azure Purview Account
* Azure Key Vault
* Azure SQL Database
* Azure Data Lake Storage Gen2 Account
* Azure Data Factory

## Control Plane (azuredeploy.bicep)

| # | Namespace | Type |
| ------------- | ------------- | ------------- |
| 1 | Microsoft.Purview | accounts |
| 2 | Microsoft.Sql | servers |
| 3 | Microsoft.Sql | servers/databases |
| 4 | Microsoft.Sql | servers/firewallRules (allow Azure services) |
| 5 | Microsoft.Sql | servers/firewallRules (allow all) |
| 6 | Microsoft.KeyVault | vaults |
| 7 | Microsoft.KeyVault | vaults/accessPolicies (Current User) |
| 8 | Microsoft.KeyVault | vaults/accessPolicies (Azure Purview MI)|
| 9 | Microsoft.KeyVault | vaults/secret (sql-secret) |
| 10 | Microsoft.Storage | storageAccounts |
| 11 | Microsoft.Storage | storageAccounts/blobServices |
| 12 | Microsoft.Storage | storageAccounts/blobServices/containers |
| 13 | Microsoft.DataFactory | factories |
| 14 | Microsoft.ManagedIdentity | userAssignedIdentities |
| 15 | Microsoft.Authorization | roleAssignments |
| 16 | Microsoft.Resources | deploymentScripts |

## Role Assignments

| # | Scope | Principal | Role Definition |
| ------------- | ------------- | ------------- | ------------- |
| 1 | Azure Purview Account | Azure Data Factory MI | Purview Data Curator |
| 2 | Azure Purview Account | Service Principal | Purview Data Curator |
| 3 | Azure Purview Account | Service Principal | Purview Data Source Administrator |
| 4 | Azure Purview Account | Current User | User Access Administrator |
| 5 | Azure Storage Account | Azure Purview MI | Storage Blob Data Reader |
| 6 | Azure Storage Account | Azure Data Factory MI | Storage Blob Data Contributor |
| 7 | Resource Group | User Assigned Identity | Contributor |

## Data Plane (purview.ps1)

1. Get Access Token
2. Azure Purview: Create Azure Key Vault Connection
3. Azure Purview: Create Credential
4. Azure SQL Database: Register Source
5. Azure SQL Database: Create Scan
6. Azure SQL Database: Run Scan
7. Azure Data Lake Storage Gen2: Load Sample Data
8. Azure Data Lake Storage Gen2: Register Source
9. Azure Data Lake Storage Gen2: Create Scan
10. Azure Data Lake Storage Gen2: Run Scan