---
title: Forgalom átirányítása a HA-alkalmazások számára – Azure CLI – Traffic Manager
description: Azure CLI-parancsfájl – példa – az alkalmazások magas rendelkezésre állásának irányítására szolgáló forgalom
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: e108fbe54ce67d4eb354319450259fc410462c53
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460613"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Forgalom irányítása az alkalmazások magas rendelkezésre állásához az Azure CLI használatával

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

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan) | Létrehoz egy App Service-csomagot. Ez olyan, mint egy kiszolgálófarm az Azure-webalkalmazáshoz. |
| [az WebApp web Create](https://docs.microsoft.com/cli/azure/webapp#az-webapp-create) | Létrehoz egy Azure-webalkalmazást a App Service tervben. |
| [az Network Traffic-Manager Profile Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile) | Létrehoz egy Azure Traffic Manager-profilt. |
| [az Network Traffic-Manager Endpoint Create](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint) | Hozzáad egy végpontot egy Azure Traffic Manager-profilhoz. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További App Service CLI-szkriptek az [Azure hálózati dokumentációjában](../cli-samples.md)találhatók.
