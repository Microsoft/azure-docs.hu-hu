---
title: Fiók hitelesítő adatainak alaphelyzetbe állítása – parancssori felület
description: Ezzel az Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc605a08147da1d076b302e515a4ebe8d411a782
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105964117"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI-példa: a fiók hitelesítő adatainak alaphelyzetbe állítása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A cikkben bemutatott Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Media Services fiókot](./create-account-howto.md).

## <a name="example-script"></a>Példaszkript

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Következő lépések

* [az AMS](/cli/azure/ams)
* [Hitelesítő adatok visszaállítása](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
