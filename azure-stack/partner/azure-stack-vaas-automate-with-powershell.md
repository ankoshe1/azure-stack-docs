---
title: Automate Azure Stack Validation with PowerShell | Microsoft Docs
description: You can automate Azure Stack Validation with PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila

ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2019

ROBOTS: NOINDEX

---

# Automate Azure Stack Validation with PowerShell

Validation as a Service (VaaS) provides the ability to automate the launching of tests using the **RunVaaSAutomation.ps1** script.

This script can be used to:

> [!div class="checklist"]
> * Install prerequisites
> * Install and start the local agent
> * Launch VaaS tests in Test Pass, Solution Validation and Package Validation workflows.
> * Report test results

Links below contains the info about how to run tests via VaaS Portal. Before using the script, you need to learn the requried parameters and their values.
  * SolutionValidation Workflow: [Validate a new Azure Stack solution](https://docs.microsoft.com/en-us/azure-stack/partner/azure-stack-vaas-validate-solution-new)
  * PackageValidation Workflow: [Validate OEM packages](azure-stack-vaas-validate-oem-package.md)
  * TestPass Workflow: [Scheduling a test](https://docs.microsoft.com/en-us/azure-stack/partner/azure-stack-vaas-schedule-test-pass)

## Download the automation scripts

1. Open an elevated PowerShell prompt.

2. Run the following script to download the automation script:

```powershell
# Review and update the $RootFolder parameter
$RootFolder = "c:\VaaS"

if (-not(Test-Path($RootFolder))) {
    mkdir $RootFolder
}
Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "$RootFolder\RunVaaSAutomation.zip"
Expand-Archive -Path "$RootFolder\RunVaaSAutomation.zip" -DestinationPath "$RootFolder\RunVaaSAutomation" -Force
Set-Location "$RootFolder\RunVaaSAutomation"
```

## Launch Solution Validation workflow

Run the following script with the appropriate parameter values:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_SolutionValidation_Configuration='Min' # enter 'Min' or 'Max'

# You don't need to moidfy the lines below
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outoutFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{}
$projectParameters = @{
    "Configuration" = $VaaSProject_SolutionValidation_Configuration;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'SolutionValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=48;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## Launch Package Validation workflow

Run the following script with the appropriate parameter values:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_PackageValidation_PackageBlobUri=''
$VaaSProject_PackageValidation_RequireDigitalSignature = "false" # enter 'true' or 'false' string
$VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs = ""
$VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs = ""

# You don't need to moidfy the lines below
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outoutFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{
    "RequireDigitalSignature" = $VaaSProject_PackageValidation_RequireDigitalSignature;
    "LocalPathtoAzureStackUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs;
    "LocalPathtoOEMUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs;
}

$projectParameters = @{
    "PackageBlobUri" = $VaaSProject_PackageValidation_PackageBlobUri;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'PackageValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable' = $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=96;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```
## Launch Test Pass workflow

Run the following script with the appropriate parameter values:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''

# You don't need to moidfy the lines below.
# Below is an example to run "Cloud Simulation Engine" test.
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outoutFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$VaaSTestFilter='Test'
$VaaSTestFilterValue='cloudsimulation'
$testParameters = @{
    'ServiceAdminUser' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUser' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUser' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DomainFQDN' = "";
    'DomainAdminUser' = "";
    'DomainAdminPassword' = "";
    'TenantId' = $stampInfo.AadTenantId;
    'ExternalFqdn' = $stampInfo.ExternalDomainFQDN;
    'NumberOfNodes' = "$($stampInfo.NumberOfNodes)";
    'Region' = $stampInfo.RegionName;
    'RunDurationInHours' = 24;
    'EnableSuBR' = "false";
    'Faults' = "";
    'Resources' = "";
    'FaultControllerSettings' = "default";
    'DiagnosticsStorageConnection' = $diagnosticsStorageConnection;
    'DiagnosticsContainerName' = "$(New-Guid).ToString().ToLower()";
    'MaxFiddlerCaptureSizeInMB' = "0";
    'PackageHashCode' = "";
}

$projectParameters = @{}

$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'TestPass';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter'= $VaaSTestFilter;
    'VaaSTestFilterValue' = $VaaSTestFilterValue;
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName' = $AgentName;
    'MaxScriptWaitTimeInHours' = 24;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## Parameter Table

For more information, see [Workflow common parameters](azure-stack-vaas-parameters.md).
    
    | Parameter | Description |
    | --- | --- |
    | VaaSAccountUserName | Your VaaS user name for VaaS Portal. |
    | VaaSAccountPassword | Your VaaS password for VaaS Portal. |
    | VaaSAccountTenantId | Your VaaS tenant GUID. |
    | ServiceAdminUserName | Your Azure Stack service admin account.  |
    | ServiceAdminPassword | Your Azure Stack service password.  |
    | TenantAdminUserName | The administrator for the primary tenant.  |
    | TenantAdminPassword | The password for the primary tenant.  |
    | CloudAdminUserName | The cloud administrator username.  |
    | CloudAdminPassword | The password for the cloud administrator.  |
    | SolutionName | The name of the VaaS solution. |
    | ProjectName | The name of the VaaS workflow. |
    | DiagnosticsStorageConnection | A SAS URL to an Azure Storage Account to which diagnostics logs will be copied during test execution. For instructions on generating the SAS URL, see [Generate the diagnostics connection string](azure-stack-vaas-parameters.md). |

## Review the results

Test logs and reports are saved under the current working folder. For other options, see [Monitor and manage tests in the VaaS portal](azure-stack-vaas-monitor-test.md).

## Next steps

To learn more about PowerShell on Azure Stack, review the latest modules.

> [!div class="nextstepaction"]
> [Azure Stack Module](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
