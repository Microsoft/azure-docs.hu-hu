---
title: Azure CLI-parancsfájl – példa – az alkalmazások magas rendelkezésre állásának irányítására szolgáló forgalom irányítása | Microsoft Docs
description: Azure CLI-parancsfájl – példa – az alkalmazások magas rendelkezésre állásának irányítására szolgáló forgalom
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
ms.openlocfilehash: 7103b11b7ee268acbddd8b402e1be1d44074f54d
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234069"
---
# <a name="route-traffic-for-high-availability-of-applications---azure-cli"></a>Forgalom irányítása az alkalmazások magas rendelkezésre állása érdekében – Azure CLI

Ez a szkript létrehoz egy erőforráscsoportot, két app Service-csomagot, két webalkalmazást, egy Traffic Manager-profilt és két Traffic Manager-végpontot. Traffic Manager a forgalmat az egyik régióban az elsődleges régióként, a másodlagos régióba irányítja, ha az elsődleges régióban lévő alkalmazás nem érhető el. A szkript végrehajtása előtt módosítania kell a MyWebApp, a MyWebAppL1 és a MyWebAppL2 értékeket az Azure-ban lévő egyedi értékekre. A szkript futtatása után az elsődleges régióban elérheti az alkalmazást az URL-mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után az alábbi paranccsal eltávolítható az erőforráscsoport, App Service az alkalmazás és az összes kapcsolódó erőforrás.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja egy erőforráscsoport, egy webalkalmazás, egy Traffic Manager-profil és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appservice plan create](/cli/azure/appservice/plan) | Létrehoz egy App Service-csomagot. Ez olyan, mint egy kiszolgálófarm az Azure-webalkalmazáshoz. |
| [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) | Létrehoz egy Azure-webalkalmazást a App Service tervben. |
| [az Network Traffic-Manager Profile Create](/cli/azure/network/traffic-manager/profile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [az Network Traffic-Manager Endpoint Create](/cli/azure/network/traffic-manager/endpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-szkriptek az [Azure hálózati dokumentációjában](../cli-samples.md)találhatók.