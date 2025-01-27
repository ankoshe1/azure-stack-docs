---
title: ASDK Release Notes 
description: Improvements, fixes, and known issues for Azure Stack Development Kit (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 12/06/2021
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/10/2020

# Intent: As an ASDK user, I want to know the latest changes, updates, and bug fixes to the ASDK.
# Keyword: asdk release notes

---


# ASDK release notes

This article provides information about changes, fixes, and known issues in the Azure Stack Development Kit (ASDK). If you're not sure which version you're running, [use the portal to check](../operator/azure-stack-updates.md).

Stay up-to-date with what's new in the ASDK by subscribing to the ![RSS](./media/asdk-release-notes/feed-icon-14x14.png) [RSS feed](/api/search/rss?locale=en-us&search=ASDK+release+notes).

::: moniker range="azs-2108"
## Build 1.2108.2.65

### New features

- For a list of fixed issues, changes, and new features in this release, see the relevant sections in the [Azure Stack release notes](../operator/release-notes.md).

### Fixed and known issues

- Backup and restore validation using the ASDK requires an increase to the VHD size. For more information, see [Validate an Azure Stack backup](asdk-validate-backup.md).
- SQL RP and MySQL RP are no longer supported in the ASDK.
- Metrics on the portal showing capacity information are no longer available due to product changes.

::: moniker-end

::: moniker range="azs-2102"
## Build 1.2102.28.82

### New features

- For a list of fixed issues, changes, and new features in this release, see the relevant sections in the [Azure Stack release notes](../operator/release-notes.md).

### Fixed and known issues

::: moniker-end

::: moniker range="azs-2008"
## Build 1.2008.0.59

### New features

- For a list of fixed issues, changes, and new features in this release, see the relevant sections in the [Azure Stack release notes](../operator/release-notes.md).

### Fixed and known issues

::: moniker-end

::: moniker range="azs-2005"
## Build 1.2005.0.40

### New features

- For a list of fixed issues, changes, and new features in this release, see the relevant sections in the [Azure Stack release notes](../operator/release-notes.md).

### Fixed and known issues

- The decryption certification password is a new option to specify the password for the self-signed certificate (.pfx) that contains the private key necessary to decrypt backup data. This password is required only if the backup is encrypted using a certificate.
- Fixed an issue that caused cloud recovery to fail if the original external certificate password changed on the multi-node source system. 
- For a list of Azure Stack known issues in this release, see the [known issues](../operator/known-issues.md) article.
- Note that available Azure Stack hotfixes are not applicable to the ASDK.

#### Initial configuration fails in ASDK

- When deploying the ASDK, you may receive the error messages **Status of 'Deployment-Phase0-DeployBareMetal' is 'Error'** and **Status of 'Deployment-InitialSteps' is 'Error'**.

- As a workaround:

1. Open the file at C:\CloudDeployment\Roles\PhysicalMachines\Tests\BareMetal.Tests.ps1 in any editor with a line counter, such as PowerShell ISE.

2. Replace line 822 with:

   ```powershell

   PartNumber = if($_.PartNumber) {$_.PartNumber.Trim()} else {""};

   ```  
::: moniker-end
