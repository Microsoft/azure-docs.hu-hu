---
title: 'CLI: Alkalmazásfájlok üzembe helyezése FTP használatával'
description: Megtudhatja, hogyan automatizálhatja az alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a példa bemutatja, hogyan hozhat létre alkalmazást, és hogyan helyezhet üzembe fájlokat FTP-vel.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 875192b5da0c034f4ac92c74dd617ded79df7f45
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788006"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Hozzon létre egy App Service alkalmazást, és telepítsen fájlokat FTP-vel az Azure CLI használatával

Ez a példaszkprogram létrehoz egy alkalmazást a App Service a kapcsolódó erőforrásokkal, majd üzembe helyez egy statikus HTML-oldalt FTP használatával. Az FTP-feltöltéshez a szkript a [cURL](https://en.wikipedia.org/wiki/CURL) segédprogramot használja példaként. A fájlok feltöltéséhez bármilyen FTP-eszközt használhat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez az oktatóanyaghoz az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése 

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) | Szerezze be az elérhető alkalmazástelepítési profilok részleteit. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
