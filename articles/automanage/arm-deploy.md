---
title: Gép Azure Automanage ARM-sablonnal
description: Megtudhatja, hogyan lehet gépeket Azure Automanage egy Azure Resource Manager sablonnal.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368609"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Gép automatikus automatizáltatása egy Azure Resource Manager (ARM) sablonnal


## <a name="overview"></a>Áttekintés
Kövesse az alábbi lépéseket a gép automatikus automatizálni való beállításának ajánlott eljárásokhoz. Az alábbi ARM-sablon létrehoz egy objektumot, amely az Azure-erőforrás, amely egy, az Automanage szolgáltatásba bevetett `configurationProfileAssignment` gépet képvisel.

## <a name="prerequisites"></a>Előfeltételek
* Létre kell hoznunk egy meglévő automatikus automatizálni való fiókot. A [fiók automatikus](./automanage-account.md) létrehozására vonatkozó további információkért tekintse meg ezt a dokumentumot.
* Az automatikus  automatizálni kívánt gépeket tartalmazó erőforráscsoporton közreműködői szerepkör szükséges

## <a name="arm-template-overview"></a>AZ ARM-sablon áttekintése
Az alábbi ARM-sablon a megadott gépet az ajánlott Azure Automanage fogja használni. Az ARM-sablon részletei és az üzembe helyezés lépései a lenti ARM-sablon üzembe helyezési szakaszában [találhatók.](#arm-template-deployment)
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM-sablon üzembe helyezése
A fenti ARM-sablon létrehoz egy konfigurációs profil-hozzárendelést a megadott géphez egy megadott automatikus fiók használatával. Ha még nem hozott létre automatikus automatizálható fiókot, további információt ebben a [dokumentumban olvashat.](./automanage-account.md)

Az `configurationProfileAssignment` érték a következő értékek egyike lehet:
* "Éles"
* "DevTest"

Az ARM-sablon üzembe helyezéséhez kövesse az alábbi lépéseket:
1. Mentse az alábbi ARM-sablont a következőként: `azuredeploy.json`
1. Az ARM-sablon üzembe helyezésének futtatása a következővel: `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Amikor a rendszer kéri, adja meg a machineName, automanageAccountName és configurationProfileAssignment értékeit
1. Kész is van!

Mint minden ARM-sablon, a paramétereket egy külön fájlban is fel lehet használni argumentumként az `azuredeploy.parameters.json` üzembe helyezéskor.

## <a name="next-steps"></a>Következő lépések
További információ a Linux és [Windows](./automanage-linux.md) rendszerek automatikus [frissítésének funkcióról](./automanage-windows-server.md)