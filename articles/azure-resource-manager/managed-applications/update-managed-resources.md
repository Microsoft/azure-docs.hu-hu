---
title: Kezelt erőforrások frissítése
description: Útmutatás a felügyelt erőforráscsoport erőforrásainak az Azure által felügyelt alkalmazásokhoz való működéséhez.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651214"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Az Azure Managed Application felügyelt erőforráscsoport erőforrásainak használata

Ez a cikk a felügyelt alkalmazás részeként üzembe helyezett erőforrások frissítését ismerteti. Egy felügyelt alkalmazás közzétevője hozzáférhet a felügyelt erőforráscsoport erőforrásaihoz. Ezen erőforrások frissítéséhez meg kell keresnie a felügyelt alkalmazáshoz társított felügyelt erőforráscsoportot, és hozzá kell férnie az adott erőforráscsoport erőforrásához.

Ez a cikk azt feltételezi, hogy telepítette a felügyelt alkalmazást a [felügyelt webalkalmazásban (IaaS) az Azure felügyeleti szolgáltatások](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) minta-projekttel. A felügyelt alkalmazás tartalmaz egy **Standard_D1_v2** virtuális gépet. Ha még nem telepítette a felügyelt alkalmazást, továbbra is használhatja ezt a cikket, és megismerheti a felügyelt erőforráscsoport frissítésének lépéseit.

Az alábbi képen a telepített felügyelt alkalmazás látható.

![Telepített felügyelt alkalmazás](./media/update-managed-resources/deployed.png)

Ebben a cikkben az Azure CLI-t használja a következőhöz:

* A felügyelt alkalmazás azonosítása
* A felügyelt erőforráscsoport azonosítása
* A virtuális gép erőforrás (ok) azonosítása a felügyelt erőforrás csoportban
* Módosítsa a virtuális gép méretét (akár kisebb méretre, ha nincs használatban, vagy egy nagyobb, ha több terhelést is támogat)
* Rendeljen hozzá egy szabályzatot a felügyelt erőforráscsoporthoz, amely meghatározza az engedélyezett helyeit.

## <a name="get-managed-application-and-managed-resource-group"></a>Felügyelt alkalmazás és felügyelt erőforráscsoport beolvasása

A felügyelt alkalmazások erőforráscsoporthoz való beszerzéséhez használja a következőt:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

A felügyelt erőforráscsoport AZONOSÍTÓjának lekéréséhez használja a következőt:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Virtuális gépek átméretezése felügyelt erőforráscsoporthoz

A felügyelt erőforráscsoport virtuális gépei megjelenítéséhez adja meg a felügyelt erőforráscsoport nevét.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

A virtuális gépek méretének frissítéséhez használja a következőt:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

A művelet befejezése után ellenőrizze, hogy az alkalmazás a standard D2 v2-ben fut-e.

![Felügyelt alkalmazás standard D2 V2 használatával](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Házirend alkalmazása a felügyelt erőforráscsoporthoz

Szerezze be a felügyelt erőforráscsoportot, és rendelje hozzá a szabályzatot az adott hatókörhöz. A szabályzat **e56962a6-4747-49cd-b67b-bf8b01975c4c** az engedélyezett helyszínek megadására szolgáló beépített szabályzat.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Az engedélyezett helyszínek megtekintéséhez használja a következőt:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

A szabályzat-hozzárendelés megjelenik a portálon.

![Szabályzat-hozzárendelés megtekintése](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* A példákat lásd: [Az Azure által felügyelt alkalmazások mintavételezési projektjei](sample-projects.md).
