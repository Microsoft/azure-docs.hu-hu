---
title: 'CLI: Ütemezett biztonsági mentés létrehozása'
description: Megtudhatja, hogyan automatizálhatja a saját alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a példa bemutatja, hogyan hozhat létre ütemezett biztonsági mentést egy alkalmazáshoz.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: db1d0558f93b203af1605663533847d32afbcffb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782548"
---
# <a name="create-a-scheduled-backup-for-an-app-service-app-using-cli"></a>Ütemezett biztonsági mentés létrehozása egy App Service alkalmazáshoz a CLI használatával

Ez a példaszk szkript létrehoz egy alkalmazást a App Service kapcsolódó erőforrásokkal együtt, majd ütemezett biztonsági mentést készít hozzá. 

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
| [`az group create`](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [`az storage account create`](/cli/azure/storage/account#az_storage_account_create) | Létrehoz egy tárfiókot. |
| [`az storage container create`](/cli/azure/storage/container#az_storage_container_create) | Létrehoz egy Azure Storage-tárolót. |
| [`az storage container generate-sas`](/cli/azure/storage/container#az_storage_container_generate_sas) | Létrehoz egy SAS-tokent egy Azure Storage-tárolóhoz.  |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Létrehoz egy App Service-csomagot. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Létrehoz egy App Service alkalmazást. |
| [`az webapp config backup update`](/cli/azure/webapp/config/backup#az_webapp_config_backup_update) | Új biztonsági mentési ütemezést konfigurál egy App Service alkalmazáshoz. |
| [`az webapp config backup show`](/cli/azure/webapp/config/backup#az_webapp_config_backup_show) | Megjeleníti a biztonsági mentés ütemezését egy App Service alkalmazáshoz. |
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az_webapp_config_backup_list) | Lekérte egy alkalmazás biztonsági mentésének App Service listáját. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
