---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755412"
---
1. Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető cikkben talál.

   ```azurecli
   az login
   ```
2. Ha több Azure-előfizetéssel rendelkezik, sorolja fel a fiókhoz tartozó előfizetéseket.

   ```azurecli
   az account list --all
   ```
3. Válassza ki a használni kívánt előfizetést.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
