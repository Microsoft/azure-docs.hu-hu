---
title: Erőforráscsoport megadása a virtuális gépekhez a Azure DevTest Labs | Microsoft Docs
description: Megtudhatja, hogyan határozható meg erőforráscsoport a virtuális gépekhez egy tesztkörnyezetben a Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c6f576a20fc8fada9dd515e8ba2a266761a3e586
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377488"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Adjon meg egy erőforráscsoportot a tesztkörnyezet virtuális gépei számára a Azure DevTest Labs

A tesztkörnyezet tulajdonosaként konfigurálhatja a tesztkörnyezet virtuális gépeit egy adott erőforráscsoportban való létrehozásra. Ez a funkció a következő helyzetekben nyújt segítséget:

- Kevesebb erőforráscsoportot kell létrehoznia tesztkörnyezetek által az előfizetésben.
- A tesztkörnyezeteket a konfigurált erőforráscsoportok rögzített készletében kell működtetni.
- Az Azure-előfizetésen belül az erőforráscsoportok létrehozásához szükséges korlátozások és jóváhagyások kezelése.
- Konszolidálja az összes tesztkörnyezeti erőforrást egyetlen erőforráscsoporton belül, így leegyszerűsítheti az erőforrások nyomon követését, és szabályzatokat alkalmaz az erőforrások erőforráscsoport szintjén való kezelésére. [](../governance/policy/overview.md)

Ezzel a funkcióval egy szkript használatával megadhat egy új vagy meglévő erőforráscsoportot az Azure-előfizetésben az összes tesztkörnyezeti virtuális géphez. Jelenleg a Azure DevTest Labs api-n keresztül támogatja ezt a funkciót.

> [!NOTE]
> A DevTest Labs-tesztkörnyezetek létrehozásakor minden előfizetési korlátozás érvényes. A laborra úgy is gondolunk, mint az előfizetésében elérhető bármely más erőforrásra. Erőforráscsoportok esetén a korlát [előfizetésenként 980 erőforráscsoport.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Az Azure Portal használata
Az alábbi lépésekkel megadhat egy erőforráscsoportot a tesztkörnyezetben létrehozott összes virtuális géphez. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A **bal oldali navigációs menüben** válassza a Minden szolgáltatás lehetőséget. 
3. Válassza a **DevTest Labs** elemet a listából.
4. A tesztkörnyezetek listájából válassza ki a **tesztkörnyezetet.**  
5. A **bal oldali**  menü Beállítások szakaszában válassza a Konfiguráció és szabályzatok lehetőséget. 
6. A **bal oldali menüben** válassza a Labor beállításai lehetőséget. 
7. Válassza **a Minden virtuális gép egy erőforráscsoportban lehetőséget.** 
8. Válasszon ki egy meglévő erőforráscsoportot **a** legördülő listában (vagy) válassza az **Új** létrehozása lehetőséget, adjon nevet az erőforráscsoportnak, majd kattintson az **OK gombra.** 

    ![Válassza ki az erőforráscsoportot az összes tesztkörnyezeti virtuális géphez](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>A PowerShell használata 
Az alábbi példa bemutatja, hogyan hozhat létre minden tesztkörnyezeti virtuális gépet egy új erőforráscsoportban egy PowerShell-szkript használatával.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Hívja meg a szkriptet a következő paranccsal. ResourceGroup.ps1 az a fájl, amely az előző szkriptet tartalmazza:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Sablon Azure Resource Manager használata
Ha tesztkörnyezet létrehozásához Azure Resource Manager sablont használ, használja a **vmCreationResourceGroupId** tulajdonságot a sablon tesztkörnyezet tulajdonságainak szakaszában, az alábbi példában látható módon:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018-10-15-preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API erőforráscsoport konfigurálása tesztkörnyezeti virtuális gépekhez
Az API használata esetén a következő lehetőségek állnak rendelkezésre a tesztkörnyezet tulajdonosaként:

- Válassza ki **a tesztkörnyezet erőforráscsoportját** az összes virtuális géphez.
- Válasszon a **tesztkörnyezet** erőforráscsoportja helyett egy meglévő erőforráscsoportot az összes virtuális géphez.
- Adjon meg **egy új erőforráscsoport-nevet** az összes virtuális gép számára.
- Folytassa a meglévő viselkedést, amelyben létrejön egy erőforráscsoport a tesztkörnyezet minden virtuális gépe számára.
 
Ez a beállítás a tesztkörnyezetben létrehozott új virtuális gépekre vonatkozik. A tesztkörnyezetben a saját erőforráscsoportjukban létrehozott régebbi virtuális gépeket ez nem érinti. A tesztkörnyezetben létrehozott környezetek továbbra is a saját erőforráscsoportjukban maradnak.

Az API használata:
- Használja a **2018-10-15-preview API-verziót.**
- Ha új erőforráscsoportot ad meg, győződjön meg arról, hogy írási jogosultsággal rendelkezik **az** előfizetésében az erőforráscsoportokhoz. Ha nem rendelkezik írási engedélyekkel, az új virtuális gépek létrehozása a megadott erőforráscsoportban sikertelen lesz.
- Az API használata közben adja meg a teljes **erőforráscsoport-azonosítót.** Példa: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Győződjön meg arról, hogy az erőforráscsoport ugyanabban az előfizetésben van, mint a tesztkörnyezet. 


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 

- [Szabályzatok beállítása tesztkörnyezethez](devtest-lab-set-lab-policy.md)
- [Gyakori kérdések](devtest-lab-faq.md)
