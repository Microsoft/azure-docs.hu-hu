---
title: Azure CLI parancsfájl-minta – App Service integrálása Application Gateway használatával | Microsoft Docs
description: Azure CLI parancsfájl-minta – App Service integrálása a Application Gateway
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3820e7bf00f99a846dd2be0edeaf4248e0dfd8ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97006081"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>App Service integrálása Application Gateway a parancssori felület használatával

Ez a minta parancsfájl egy Azure App Service webalkalmazást, egy Azure-Virtual Network és egy Application Gateway hoz létre. Ezután korlátozza a webalkalmazás forgalmát, hogy csak a Application Gateway alhálózatból származzanak.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0.74 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport, App Service alkalmazás, Cosmos DB és minden kapcsolódó erőforrás létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az network vnet create`](/cli/azure/network/vnet#az-network-vnet-create) | Virtuális hálózatot hoz létre. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az-network-public-ip-create) | Egy nyilvános IP-címet hoz létre. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az-network-public-ip-show) | Nyilvános IP-cím részleteinek megjelenítése. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Létrehoz egy App Service webalkalmazást. |
| [`az webapp show`](/cli/azure/webapp#az-webapp-show) | Egy App Service webalkalmazás részleteinek megjelenítése. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az-webapp-config-access-restriction-add) | Hozzáférési korlátozást hoz létre a App Service webalkalmazáshoz. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az-network-application-gateway-create) | Létrehoz egy Application Gateway. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az-network-application-gateway-http-settings-update) | A Application Gateway HTTP-beállításokat frissíti. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
