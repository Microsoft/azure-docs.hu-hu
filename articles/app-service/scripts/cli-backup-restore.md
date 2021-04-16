---
title: 'CLI: Alkalmazás visszaállítása biztonsági másolatból'
description: Megtudhatja, hogyan automatizálhatja az alkalmazás üzembe helyezését és felügyeletét az Azure CLI App Service használatával. Ez a példa bemutatja, hogyan állítható vissza egy alkalmazás egy biztonsági másolatból.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: b30530956b6e8a4b7f10393cf90caf341bcc82a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483401"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Webalkalmazás visszaállítása biztonsági másolatból a CLI használatával

Ez a példaszkript egy webalkalmazást hoz létre az App Service-ben a kapcsolódó erőforrásokkal együtt, majd egyszeri biztonsági mentést hajt végre. 

A szkript futtatásához szükség van egy webalkalmazás meglévő biztonsági másolatára. Ennek létrehozásáról lásd: [Webalkalmazások biztonsági mentése](cli-backup-onetime.md) vagy [Ütemezett biztonsági másolat létrehozása egy webalkalmazásról](cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, az Azure CLI 2.0-s vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup#az-webapp-config-backup-list) | Lekéri egy webalkalmazás biztonsági másolatainak listáját. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup#az-webapp-config-backup-restore) | Visszaállít egy webalkalmazást egy biztonsági másolatból. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További App Service CLI-példaszkripteket az [Azure App Service dokumentációjában](../samples-cli.md) találhat.
