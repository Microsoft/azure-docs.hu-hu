---
title: Erőforráscsoport megadása a Azure DevTest Labs virtuális gépekhez | Microsoft Docs
description: Megtudhatja, hogyan határozhat meg egy erőforráscsoportot a virtuális gépekhez Azure DevTest Labs-ban laborban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483363"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Válasszon erőforráscsoportot a tesztkörnyezet virtuális gépei számára Azure DevTest Labs

A labor tulajdonosaként beállíthatja, hogy a tesztkörnyezet virtuális gépei egy adott erőforráscsoporthoz jöjjenek létre. Ez a funkció a következő helyzetekben nyújt segítséget:

- A laborok kevesebb erőforráscsoportot hoztak létre az előfizetésében.
- A laborok az Ön által konfigurált erőforráscsoport-csoporton belül működnek.
- Az Azure-előfizetéshez tartozó erőforráscsoportok létrehozásához szükséges korlátozások és jóváhagyások megkerülő megoldás.
- Egyesítse az összes laboratóriumi erőforrást egy adott erőforráscsoporthoz, hogy leegyszerűsítse ezeknek az erőforrásoknak a nyomon követését, és [házirendeket](../governance/policy/overview.md) alkalmazzon az erőforrások erőforráscsoport szintjén történő kezeléséhez.

Ezzel a funkcióval egy parancsfájl segítségével megadhat egy új vagy egy meglévő erőforráscsoportot az Azure-előfizetésében az összes Tesztkörnyezet virtuális gépe számára. A Azure DevTest Labs jelenleg egy API-n keresztül támogatja ezt a funkciót.

> [!NOTE]
> A DevTest Labs szolgáltatásban a Labs létrehozásakor minden előfizetési korlát érvényes. Gondoljon egy laborra, mint bármely más erőforrásra az előfizetésében. Erőforráscsoportok esetén a korlát [980 erőforráscsoport/előfizetés](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Az Azure Portal használata
Kövesse az alábbi lépéseket a laborban létrehozott összes virtuális géphez tartozó erőforráscsoport megadásához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget. 
3. Válassza a **DevTest Labs** elemet a listából.
4. A Labs listából válassza ki a **labort**.  
5. A bal oldali menüben a **Beállítások** szakaszban válassza a **konfiguráció és szabályzatok** lehetőséget. 
6. A bal oldali menüben válassza a **labor-beállítások** lehetőséget. 
7. Válassza ki **az összes virtuális gépet egy erőforráscsoporthoz**. 
8. Válasszon ki egy meglévő erőforráscsoportot a legördülő listából (vagy) válassza az **új létrehozása** lehetőséget, adja meg az erőforráscsoport **nevét** , majd kattintson **az OK gombra**. 

    ![Válassza ki az erőforráscsoportot az összes laboratóriumi virtuális gép számára](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>A PowerShell használata 
Az alábbi példa azt szemlélteti, hogyan használható egy PowerShell-szkript az összes Tesztkörnyezet virtuális gép létrehozásához egy új erőforráscsoporthoz.

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

Hívja meg a szkriptet a következő parancs használatával. ResourceGroup.ps1 az előző parancsfájlt tartalmazó fájl:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager-sablon használata
Ha Azure Resource Manager sablont használ a labor létrehozásához, használja a **vmCreationResourceGroupId** tulajdonságot a sablon labor tulajdonságai szakaszában, az alábbi példában látható módon:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API egy erőforráscsoport laboratóriumi virtuális gépekhez való konfigurálásához
Az API használatakor a következő lehetőségek közül választhat labor-tulajdonosként:

- Válassza ki a **tesztkörnyezet erőforrás-csoportját** az összes virtuális géphez.
- Válasszon egy **meglévő erőforráscsoportot** , amely nem a labor erőforráscsoport az összes virtuális géphez.
- Adjon meg egy **Új erőforráscsoport** -nevet az összes virtuális géphez.
- Folytassa a meglévő viselkedést, amelyben egy erőforráscsoport jön létre a laborban található minden egyes virtuális géphez.
 
Ez a beállítás a laborban létrehozott új virtuális gépekre vonatkozik. A laborban a saját erőforráscsoportok létrehozott régebbi virtuális gépek érintetlenek maradnak. A laborban létrehozott környezetek továbbra is a saját erőforráscsoportok maradnak.

Az API használata:
- Az API-verzió **2018_10_15_preview** használata.
- Ha új erőforráscsoportot ad meg, győződjön meg arról, hogy rendelkezik **írási engedéllyel** az előfizetésben található erőforráscsoportok számára. Ha nem rendelkezik írási engedélyekkel, az új virtuális gépek létrehozása a megadott erőforráscsoporthoz sikertelen lesz.
- Az API használata közben adja át a **teljes erőforráscsoport-azonosítót**. Példa: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Győződjön meg arról, hogy az erőforráscsoport ugyanabban az előfizetésben van, mint a labor. 


## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket: 

- [Tesztkörnyezet szabályzatának beállítása](devtest-lab-set-lab-policy.md)
- [Gyakori kérdések](devtest-lab-faq.md)
