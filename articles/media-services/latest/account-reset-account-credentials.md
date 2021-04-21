---
title: Fiók hitelesítő adatainak alaphelyzetbe állítása – CLI
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
ms.openlocfilehash: d5604f177484d33255d2923d72b00fae124c0f9a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783592"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Azure CLI-példa: Fiók hitelesítő adatainak alaphelyzetbe állítása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A cikkben bemutatott Azure CLI-szkripttel alaphelyzetbe állíthatja a fiókja hitelesítő adatait, és lekérheti az app.config beállításokat.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Media Services-fiókot.](./create-account-howto.md)

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

* [az ams](/cli/azure/ams)
* [Hitelesítő adatok visszaállítása](/cli/azure/ams/account/sp#az_ams_account_sp_reset_credentials)
