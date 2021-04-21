---
title: E-mail küldése Azure Automation runbookból
description: Ez a cikk bemutatja, hogyan küldhet e-mailt egy runbookból.
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2650e3a9ce58b611c1aff1a569cc1e8f0980fd4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833489"
---
# <a name="send-an-email-from-a-runbook"></a>E-mail küldése runbookból

A [SendGriddel](https://sendgrid.com/solutions) e-mailt küldhet egy Runbookból a PowerShell használatával. 

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, aktiválhatja [MSDN-előfizetői](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) előnyeit, vagy regisztrálhat egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Egy SendGrid-fiók.](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account)
* [Automation-fiók](./index.yml) **az Az modulokkal.**
* [Run As-fiók a](./automation-security-overview.md#run-as-accounts) runbook tárolására és végrehajtására.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

A következő PowerShell-Azure Key Vault hozhat létre új alkalmazásokat. Cserélje le a változóértékeket a környezetére jellemző értékekre. A beágyazott Azure Cloud Shell a kódblokk jobb felső sarkában található **Try It (Próbálja** ki) gombbal. Helyileg is másolhatja és futtathatja a kódot, ha az [Az modulok](/powershell/azure/install-az-ps) telepítve vannak a helyi gépen.

> [!NOTE]
> Az API-kulcs lekérése a SendGrid API-kulcs megkeresését [lehetővé tő lépésekkel olvasható.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key)

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

A titkos adatok létrehozására és a titkos Azure Key Vault további módjaiért tekintse meg a Key Vault [útmutatóit.](../key-vault/index.yml)

## <a name="import-required-modules-into-your-automation-account"></a>A szükséges modulok importálása az Automation-fiókba

Az Azure Key Vault runbookban való használathoz importálni kell a következő modulokat az Automation-fiókjába:

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Útmutatásért lásd: [Az modulok importálása.](shared-resources/modules.md#import-az-modules)

## <a name="create-the-runbook-to-send-an-email"></a>E-mail elküldését elküldő runbook létrehozása

Miután létrehozott egy Key Vault és tárolta az API-kulcsot, ideje létrehozni azt a runbookot, amely lekéri az API-kulcsot, és elküld egy `SendGrid` e-mailt. Használjuk azt a runbookot, amely az Azure-ral való hitelesítéshez egy olyan runbookot használ, amely a következőből kér le egy `AzureRunAsConnection` titkos Azure Key Vault. [](./automation-security-overview.md#run-as-accounts) A runbooknak a **Send-GridMailMessage** lesz a neve. Módosíthatja a példaként használt PowerShell-szkriptet, és újra felhasználhatja különböző forgatókönyvekben.

1. Ugrás az Azure Automation fiókra.
2. A **Folyamatautomatizálás alatt** válassza a **Runbookok lehetőséget.**
3. A runbookok listájának tetején válassza a **+ Runbook létrehozása lehetőséget.**
4. A Runbook hozzáadása lapon a runbook neveként adja meg a **Send-GridMailMessage** nevet. A runbook típusaként válassza a **PowerShell lehetőséget.** Ezután kattintson a **Létrehozás** elemre.
   ![Runbook létrehozása](./media/automation-send-email/automation-send-email-runbook.png)
5. Létrejön a runbook, és megnyílik a PowerShell-runbook szerkesztése oldal.
   ![A runbook szerkesztése](./media/automation-send-email/automation-send-email-edit.png)
6. Másolja az alábbi PowerShell-példát a Szerkesztés lapra. Győződjön meg arról, hogy a adja meg a kiválasztott nevet `VaultName` a Key Vault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>&quot;
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name &quot;SendGridAPIKey").SecretValue
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. A **runbook mentéshez** és közzétételéhez válassza a Közzététel lehetőséget.

A runbook sikeres végrehajtásának ellenőrzéséhez kövesse [a Runbook](manage-runbooks.md#test-a-runbook) tesztelése vagy a [Runbook futtatása alatt található lépéseket.](start-runbooks.md)

Ha kezdetben nem látja a teszt e-mailt, ellenőrizze a **Levélszemét és** a **Levélszemét mappát.**

## <a name="clean-up-resources-after-the-email-operation"></a>Erőforrások tisztítása az e-mail-művelet után

1. Ha a runbookra már nincs szükség, jelölje ki a runbookok listájában, és kattintson a **Törlés gombra.**

2. Törölje a Key Vault a [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault) parancsmag használatával.

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések

* Runbook-feladat adatainak Log Analytics-munkaterületre való elküldését lásd: Azure Automation feladatadatok továbbítása Azure Monitor [naplókba.](automation-manage-send-joblogs-log-analytics.md)
* Alapszintű metrikák és naplók figyelése: Riasztás használata [runbook Azure Automation aktiválására.](automation-create-alert-triggered-runbook.md)
* A Runbook-műveletek során felmerülő problémák elhárításához lásd: [Runbook-problémák elhárítása.](./troubleshoot/runbooks.md)
