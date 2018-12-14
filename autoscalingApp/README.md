# Purpose

This folder holds Azure Web App job for monitoring and autoscaling Azure Agents Virtual Machines Scale Set.

## Implementation

Azure Web app job checks Azure DevOps account specified and monitors queue. As soon as there is waiting job - new VM in VMSS shall be started.
If queue is empty and we have more than 1 VM running in VMSS - extra VMs shall be stopped. If there is 1 VM in VMSS and it is running for 1 hour without a jobs - it shall be deprovisioned as well.

Job shall check maximum amount of possible private agents in account and ensure that VMSS have this amount of VMs provisioned (faster startup times, though we'll pay extra for disk drives).

Job shall log start/stop attempts in external storage for future ML model training. 

Job is written on C#, as it is easier than Powershell for me :)

## Settings 

Currently, settings are set in App.config (or one can use ARM template in arm-template folder and deploy them as appsettings in Azure Web app).

```Agents_PoolName``` - specify pool name to monitor here (or set ```Agents_PoolId```)

```Agents_PoolId``` - if ```Agents_PoolName``` is not specified, set ID here

```Azure_DevOpsInstance``` - specify you Azure Devops instance name (first segment after hostname https://dev.azure.com/). Example: ```https://dev.azure.com/testusername/``` - here your instance name is ```testusername```

```Azure_DevOpsPAT``` - personal access token for Azure DevOps. See https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts 

```Azure_ServicePrincipleClientId``` - Azure Service Principle ID (must have at least Contribute on VMSS which hosts agents)

```Azure_ServicePrincipleClientSecret``` - Azure Service Principle Client Secret

```Azure_ServicePrincipleTenantId``` - Azure Service Principle Tenant ID

```Azure_SubscriptionId``` - Azure Subscription, where VMSS with agents is hosted

```Azure_VMSS_resourceGroupName``` - resource group name, which hosts VMSS

```Azure_VMSS_Name``` - Azure VMSS name

```DryRunExecution``` - if set to true, then now actual (de)provision actions will be taken against VMSS; ```Azure_Storage_ActionsTracking_TableName``` will be appended with ```DryRun``` to not mangle with actual run data

```Azure_Storage_ConnectionString``` - Azure Storage connection string to store runtime data for monitoring and possible ML usage (who knows). If empty - data will not be stored

```Azure_Storage_ActionsTracking_TableName``` - table name to store runtime data in. If empty and connection string ```Azure_Storage_ConnectionString``` specified - then it will default to ```DefaultTrackingTable```.

As will all Web Jobs - you need to specify connection strings to Azure Storage (they are used behind the scenes for logging and time tracking).

They must be specified in following connection strings: ```AzureWebJobsDashboard``` and ```AzureWebJobsStorage```.
