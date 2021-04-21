---
title: 'CLI: Üzembe helyezés előkészítési pontra'
description: Megtudhatja, hogyan automatizálhatja a saját alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a példa bemutatja, hogyan helyezhet üzembe kódot egy előkészítési pontra.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 2b995dcd-e471-4355-9fda-00babcdb156e
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: bba2c3d6faf99962ce4e687832c4d26dcd3dc671
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787853"
---
# <a name="create-an-app-service-app-and-deploy-code-to-a-staging-environment-using-azure-cli"></a>Alkalmazás App Service és kód üzembe helyezése átmeneti környezetben az Azure CLI használatával

Ez a példaszk szkript létrehoz egy alkalmazást a App Service egy "előkészítés" nevű további üzembehelyi pont használatával, majd üzembe helyez egy mintaalkalmazást az "előkészítési" ponton.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.sh "Create an app and deploy code to a staging environment")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp deployment slot create`](/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_create) | Létrehoz egy üzembehelyezési pontot. |
| [`az webapp deployment source config`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config) | Társít egy App Service egy Git- vagy Mercurial-adattárhoz. |
| [`az webapp deployment slot swap`](/cli/azure/webapp/deployment/slot#az_webapp_deployment_slot_swap) | Egy megadott üzembehelyezési pontot az éles környezetbe helyez át. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
