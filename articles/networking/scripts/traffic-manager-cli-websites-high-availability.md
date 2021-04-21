---
title: Azure CLI-példaszkret – Forgalom útválasztása az alkalmazások magas rendelkezésre állásának | Microsoft Docs
description: Azure CLI-példaszkret – Forgalom útválasztása az alkalmazások magas rendelkezésre állása érdekében
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 06/26/2018
ms.author: allensu
ms.openlocfilehash: 00964a49940b208144167e9fe1ff4248f46e35fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763198"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Forgalom útválasztása az alkalmazások magas rendelkezésre állásának érdekében – Azure CLI

Ez a szkript létrehoz egy erőforráscsoportot, két App Service-tervet, két webalkalmazást, egy Traffic Manager-profilt és két Traffic Manager-végpontot. Traffic Manager forgalmat az alkalmazáshoz irányítja elsődleges régióként, illetve a másodlagos régióba, ha az elsődleges régióban található alkalmazás nem érhető el. A szkript végrehajtása előtt a MyWebApp, MyWebAppL1 és MyWebAppL2 értékeket egyedi értékekre kell módosítania az Azure-ban. A szkript futtatása után az elsődleges régióban található alkalmazást az URL-cím használatával mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A szkriptminta futtatása után a következő paranccsal eltávolítható az erőforráscsoport, az App Service és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy webalkalmazás, egy Traffic Manager-profil és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appservice plan create](/cli/azure/appservice/plan) | Létrehoz egy App Service-csomagot. Ez olyan, mint az Azure-webalkalmazás kiszolgálófarmja. |
| [az webapp create](/cli/azure/webapp#az_webapp_create) | Létrehoz egy Azure-webalkalmazást a App Service tervben. |
| [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-szkriptmintákat a [dokumentációban Azure-hálózatkezelés találhat.](../cli-samples.md)