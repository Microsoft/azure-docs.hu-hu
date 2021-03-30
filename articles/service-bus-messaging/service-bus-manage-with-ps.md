---
title: Azure Service Bus-erőforrások kezelése a PowerShell használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet Service Bus entitásokat (névtereket, várólistákat, témákat, előfizetéseket) a Azure PowerShell modul használatával.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b6439deb2b86c2ea5b50fe3bdbad89a0875b2acc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88065743"
---
# <a name="use-powershell-to-manage-service-bus-resources"></a>Service Bus erőforrások kezelése a PowerShell használatával

Microsoft Azure PowerShell az Azure-szolgáltatások üzembe helyezésének és felügyeletének vezérlésére és automatizálására használható parancsfájlkezelési környezet. Ez a cikk azt ismerteti, hogyan használható a [Service Bus Resource Manager PowerShell-modul](/powershell/module/az.servicebus) Service Bus entitások (névterek, várólisták, témakörök és előfizetések) egy helyi Azure PowerShell konzol vagy parancsfájl használatával történő kiépítéséhez és kezeléséhez.

Service Bus entitásokat Azure Resource Manager sablonok használatával is kezelheti. További információkért tekintse meg a [Service Bus erőforrások létrehozása Azure Resource Manager sablonok használatával](service-bus-resource-manager-overview.md)című cikket.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Az előfizetés beszerzésével kapcsolatos további információkért lásd a [vásárlási lehetőségek][purchase options], a [tagok ajánlatai][member offers]vagy az [ingyenes fiók][free account]lehetőséget.
* Azure PowerShell-t futtató számítógép. Útmutatásért lásd: [Azure PowerShell-parancsmagok első lépései](/powershell/azure/get-started-azureps).
* A PowerShell-parancsfájlok, a NuGet-csomagok és a .NET-keretrendszer általános ismerete.

## <a name="get-started"></a>Bevezetés

Első lépésként a PowerShell használatával jelentkezzen be az Azure-fiókjába és az Azure-előfizetésbe. Az Azure-fiókjába való bejelentkezéshez és az Azure-előfizetésében található erőforrások lekéréséhez és eléréséhez kövesse az első [lépések a Azure PowerShell-parancsmagokkal](/powershell/azure/get-started-azureps) című témakör utasításait.

## <a name="provision-a-service-bus-namespace"></a>Service Bus névtér kiépítése

Service Bus névterek használatakor a [Get-AzServiceBusNamespace](/powershell/module/az.servicebus/get-azservicebusnamespace), a [New-AzServiceBusNamespace](/powershell/module/az.servicebus/new-azservicebusnamespace), a [Remove-AzServiceBusNamespace](/powershell/module/az.servicebus/remove-azservicebusnamespace)és a [set-AzServiceBusNamespace](/powershell/module/az.servicebus/set-azservicebusnamespace) parancsmagot használhatja.

Ez a példa néhány helyi változót hoz létre a parancsfájlban; `$Namespace` és `$Location` .

* `$Namespace` annak a Service Bus névtérnek a neve, amellyel dolgozni szeretne.
* `$Location` azonosítja azt az adatközpontot, amelyben kiépítjük a névteret.
* `$CurrentNamespace` a lekért (vagy létrehozandó) hivatkozási névteret tárolja.

Egy tényleges parancsfájlban, `$Namespace` és `$Location` paraméterként adható át.

A szkript ezen része a következő műveleteket végzi el:

1. Megkísérli beolvasni egy Service Bus névteret a megadott névvel.
2. Ha a névtér található, akkor a rendszer jelentést készít arról, hogy mi található.
3. Ha a névtér nem található, létrehozza a névteret, majd lekéri az újonnan létrehozott névteret.
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>Névtér-engedélyezési szabály létrehozása

Az alábbi példa bemutatja, hogyan kezelhetők a névtér-engedélyezési szabályok a [New-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/new-azservicebusauthorizationrule), a [Get-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/get-azservicebusauthorizationrule), a [set-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/set-azservicebusauthorizationrule)és a [Remove-AzServiceBusAuthorizationRule](/powershell/module/az.servicebus/remove-azservicebusauthorizationrule) parancsmagok használatával.

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzServiceBusKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzServiceBusAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -Name $AuthRule
}
```

## <a name="create-a-queue"></a>Üzenetsor létrehozása

Üzenetsor vagy témakör létrehozásához hajtson végre egy névtér-ellenőrzést az előző szakaszban található parancsfájl használatával. Ezután hozza létre a várólistát:

```powershell
# Check if queue already exists
$CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>Várólista tulajdonságainak módosítása

Az előző szakaszban leírt parancsfájl végrehajtása után a [set-AzServiceBusQueue](/powershell/module/az.servicebus/set-azservicebusqueue) parancsmaggal frissítheti egy üzenetsor tulajdonságait, ahogy az alábbi példában is látható:

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>Más Service Bus entitások kiépítés

Más entitások, például témakörök és előfizetések kiépítéséhez használhatja a [Service Bus PowerShell-modult](/powershell/module/az.servicebus) . Ezek a parancsmagok szintaktikai módon hasonlóak az előző szakaszban bemutatott üzenetsor-létrehozási parancsmagokhoz.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a teljes Service Bus Resource Manager PowerShell- [modul dokumentációját](/powershell/module/az.servicebus). Ezen az oldalon az összes elérhető parancsmag szerepel.
- Azure Resource Manager sablonok használatával kapcsolatos információkért tekintse meg [Service Bus erőforrások létrehozása Azure Resource Manager sablonok használatával](service-bus-resource-manager-overview.md)című cikket.
- Információk a [Service Bus .net felügyeleti könyvtárairól](service-bus-management-libraries.md).

Az Service Bus entitások kezelésének további módjai a következő blogbejegyzésekben olvashatók:

* [Service Bus várólisták, témakörök és előfizetések létrehozása PowerShell-parancsfájl használatával](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
* [Service Bus névtér és az Event hub létrehozása PowerShell-parancsfájl használatával](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)
* [PowerShell-parancsfájlok Service Bus](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: https://azure.microsoft.com/pricing/purchase-options/
[member offers]: https://azure.microsoft.com/pricing/member-offers/
[free account]: https://azure.microsoft.com/pricing/free-trial/