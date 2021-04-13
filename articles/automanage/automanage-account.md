---
title: Azure Automanage fiók
description: Megtudhatja, hogyan működik az automatikus fiók, és hogyan hozhat létre egyet.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: b79e061ae00c42ed2ec2ac39f5653a868f09a15f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368697"
---
# <a name="automanage-accounts"></a>Fiókok automatikusmaninálása

Az Automanage-fiók az az identitás, amelyet az automatikus szolgáltatás az automatikus műveletek végrehajtásához használ.

A Azure Portal esetén, amikor engedélyezi az automatikus szolgáltatásokat a virtuális gépeken, az **Azure-beli** virtuális gépek engedélyezése ajánlott eljárás panelen található Speciális legördülő menüben hozzárendelheti vagy manuálisan létrehozhatja az automatikus rendszergazdai fiókot.

Az automatikus kezelőfiók közreműködői  és  erőforrás-szabályzat-közreműködői szerepkört is kap az automatikus használatra előszabadott gépet tartalmazó előfizetés(ék)hez. Használhatja ugyanazt az automatikus kezelőfiókot több előfizetésben lévő gépeken,  így  a fiók közreműködője és az erőforrás-szabályzat közreműködője jogosultságot biztosít az összes előfizetéshez.

Ha a virtuális gép egy másik előfizetésben lévő Log Analytics-munkaterülethez  csatlakozik, az automatikus fiók közreműködői és erőforrás-szabályzat-közreműködői jogosultságot is kap a másik előfizetésben. 

Ha egy új automatikus felügyeleti fiókkal engedélyezi az automatikus adatokat, a következő engedélyekkel kell rendelkeznie az előfizetésen: Tulajdonosi szerepkör vagy Közreműködő, valamint felhasználói hozzáférés-rendszergazdai szerepkörök.   

Ha egy meglévő automatikus kezelőfiókkal engedélyezi az automatikus adatokat, a közreműködői szerepkörre lesz szüksége a virtuális gépeket tartalmazó erőforráscsoporton. 

> [!NOTE]
> Ha letiltja az automatikus manage ajánlott eljárásokat, az automatikus fiókengedélyek megmaradnak a társított előfizetések esetén is. Manuálisan távolítsa el az engedélyeket az előfizetés IAM-oldalára, vagy törölje az automatikus fiók-adatokat. Az automatikus felügyelet fiókja nem törölhető, ha továbbra is gépeket kezel.

## <a name="create-an-automanage-account"></a>Automatikus fiók létrehozása
Létrehozhat egy automatikus automatizálni kívánt fiókot a portálon vagy egy ARM-sablonnal.

### <a name="portal"></a>Portál
1. Lépjen a **portál Automatikus manage** (Automatikus automatizálás) paneljével
1. Kattintson **az Engedélyezés gombra a meglévő gépen**
1. A **Speciális alatt** kattintson az "Új fiók létrehozása" elemre.
1. Töltse ki a kötelező mezőket, és kattintson a Létrehozás **gombra.**

### <a name="arm-template"></a>ARM-sablon
Az automatikus fiók arm-sablonnal való létrehozásához 2 lépés szükséges:
1. Az automatikus fiók létrehozása
1. Adjon megfelelő engedélyeket a fióknak, hogy lehetővé tegye a számára a műveletek elvégzését
    1. Szüksége lesz a lépéshez létrehozott fiók objektumazonosítójára.
        1. A fiók szolgáltatásnévvel (beleértve az objektumazonosítót) kapcsolatos részleteket itt [találja.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal#view-the-service-principal)
    1. Miután megtalálta a szolgáltatásnévvel, másolja ki az **objektumazonosítót.** Mentse ezt, mert az engedélyek delegálálhoz az alábbiakban szükség lesz rá.

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. Automatikus fiók létrehozása (nem ad engedélyeket a fióknak)
Automatikus fiók létrehozásához mentse a következő ARM-sablont a következőként, és futtassa az `azuredeploy.json` alábbi Azure CLI-parancsot. Ha végzett, lépjen az alábbi második sablonra, hogy megfelelő engedélyeket delegáljon a fióknak.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. Engedélyek megadása az automatikus fiókhoz
Ahhoz, hogy megfelelő engedélyeket biztosítson az automatikus fiók számára, a következőket kell megtennie:
1. Mentse a következő ARM-sablont `azuredeploy2.json` másként, és futtassa az alábbi Azure CLI-parancsot.
1. Amikor a rendszer kéri, adja meg a létrehozott és mentett automatikus fiók objektumazonosítóját.

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('contributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(variables('resourcePolicyContributorRoleDefinitionID'))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések
* További információ a Linux és [Windows](./automanage-linux.md) rendszerek automatikus szolgáltatásmanitálásról [](./automanage-windows-server.md)