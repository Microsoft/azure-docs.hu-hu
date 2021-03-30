---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67178852"
---
 Ha a Azure Cloud Shell használja, a "kipróbálás" gombra kattintás után automatikusan bejelentkezhet az Azure-fiókjába. A helyi bejelentkezéshez nyissa meg emelt szintű jogosultságokkal a PowerShell-konzolt, és futtassa a parancsmagot a kapcsolódáshoz.

```azurepowershell
Connect-AzAccount
```

Ha egynél több előfizetéssel rendelkezik, szerezze be az Azure-előfizetések listáját.

```azurepowershell-interactive
Get-AzSubscription
```

Válassza ki a használni kívánt előfizetést.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```