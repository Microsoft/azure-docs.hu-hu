---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92755788"
---
Jelentkezzen be az Azure-előfizetésbe az [az login](/cli/azure/) paranccsal, és kövesse a képernyőn látható utasításokat. További információt a bejelentkezésről az [Azure CLI használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető cikkben talál.

```azurecli
az login
```

Ha több Azure-előfizetéssel rendelkezik, sorolja fel a fiókhoz tartozó előfizetéseket.

```azurecli
az account list --all
```

Válassza ki a használni kívánt előfizetést.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
