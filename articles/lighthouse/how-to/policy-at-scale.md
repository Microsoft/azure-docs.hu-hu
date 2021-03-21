---
title: Azure Policy üzembe helyezése a delegált előfizetések számára nagy léptékben
description: Ismerje meg, hogy az Azure Lighthouse hogyan helyezheti üzembe a házirend-definíciót és a házirend-hozzárendelést több bérlő között.
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 48354c3cca7574b1d5acf71865218564591bc23e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049780"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Azure Policy üzembe helyezése a delegált előfizetések számára nagy léptékben

Szolgáltatóként több ügyfél-bérlőt is felkészített az [Azure világítótoronyba](../overview.md). Az Azure Lighthouse lehetővé teszi a szolgáltatók számára, hogy egyszerre több bérlőn is elvégezzék a műveleteket, így hatékonyabbá téve a felügyeleti feladatokat.

Ebből a témakörből megtudhatja, hogyan használhatók a [Azure Policy](../../governance/policy/index.yml) egy házirend-definíció és egy házirend-hozzárendelés több bérlőn való üzembe helyezésére a PowerShell-parancsok használatával. Ebben a példában a házirend-definíció biztosítja, hogy a rendszer csak HTTPS-forgalmat engedélyezzen a Storage-fiókok biztonságossá tételéhez.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Az Azure Resource Graph használata az ügyfél-bérlők lekérdezéséhez

Az [Azure Resource Graph](../../governance/resource-graph/index.yml) segítségével lekérdezheti a kezelt ügyfél-bérlő összes előfizetését. Ebben a példában olyan Storage-fiókokat fogunk azonosítani ezekben az előfizetésekben, amelyek jelenleg nem igényelnek HTTPS-forgalmat.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Házirend üzembe helyezése több ügyfél bérlője között

Az alábbi példa azt mutatja be, hogyan használható egy [Azure Resource Manager sablon](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) egy házirend-definíció és egy házirend-hozzárendelés központi telepítéséhez több ügyfél-bérlőben lévő delegált előfizetések között. Ennek a házirend-definíciónak a használatához minden Storage-fióknak HTTPS-forgalmat kell használnia, megakadályozva az új Storage-fiókok létrehozását, amelyek nem felelnek meg a meglévő Storage-fiókoknak, és nem megfelelő beállítás nélkül.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

> [!NOTE]
> Míg a házirendek több bérlőn is üzembe helyezhetők, jelenleg nem tekintheti meg a nem megfelelő erőforrások [megfelelőségi adatait](../../governance/policy/how-to/determine-non-compliance.md#compliance-details) ezekben a bérlők között.

## <a name="validate-the-policy-deployment"></a>Szabályzat központi telepítésének ellenőrzése

Miután telepítette a Azure Resource Manager sablont, megerősítheti, hogy a házirend-definíció alkalmazása sikeresen megtörtént, ha a **EnableHttpsTrafficOnly** beállítással **Hamis értéket** ad meg a delegált előfizetések egyikében. A házirend-hozzárendelés miatt nem lehet létrehozni ezt a Storage-fiókot.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, távolítsa el az üzemelő példány által létrehozott házirend-definíciót és hozzárendelést.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Következő lépések

- A [Azure Policy](../../governance/policy/index.yml)megismerése.
- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- Megtudhatja, hogyan [telepíthet olyan házirendet, amely szervizelhető](deploy-policy-remediation.md) egy delegált előfizetésen belül.
