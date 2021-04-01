---
title: 'CLI: ütemezett biztonsági mentés létrehozása'
description: Ismerje meg, hogyan automatizálható az App Service-alkalmazás üzembe helyezése és kezelése az Azure CLI használatával. Ez a példa bemutatja, hogyan hozhat létre ütemezett biztonsági mentést egy alkalmazáshoz.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 500ac99cd35cfdf601be75a19a1d43f84795cbe8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97006424"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Ütemezett biztonsági mentés létrehozása egy App Service-alkalmazáshoz a parancssori felület használatával

Ez a példa egy alkalmazást hoz létre App Service a kapcsolódó erőforrásokkal együtt, majd létrehoz egy ütemezett biztonsági mentést. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-scheduled/backup-scheduled.sh?highlight=3-7 "Create a scheduled backup for an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az storage account create`](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [`az storage container create`](/cli/azure/storage/container#az-storage-container-create) | Létrehoz egy Azure Storage-tárolót. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az-storage-container-generate-sas) | Létrehoz egy SAS-tokent egy Azure Storage-tárolóhoz.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup#az-webapp-config-backup-update) | Új biztonsági mentési ütemtervet konfigurál egy App Service alkalmazáshoz. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup#az-webapp-config-backup-show) | Egy App Service alkalmazás biztonsági mentési ütemtervét jeleníti meg. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az-webapp-config-backup-list) | Lekéri egy App Service alkalmazás biztonsági másolatainak listáját. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
