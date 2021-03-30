---
title: Exchange-társ létrehozása vagy módosítása a PowerShell használatával
titleSuffix: Azure
description: Exchange-társ létrehozása vagy módosítása a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 59d098f1ff0f78e37601544619c603b776e0cf22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89071754"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Exchange-társ létrehozása vagy módosítása a PowerShell használatával

Ez a cikk azt ismerteti, hogyan hozható létre Microsoft Exchange-társ a PowerShell-parancsmagok és a Resource Manager-alapú üzemi modell használatával. A cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, ezt az útmutatót az Azure [Portalon](howto-exchange-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítási útmutatót.
* Ha már rendelkezik olyan Exchange-partnerekkel, amelyek nem az Azure-erőforrásokra lettek konvertálva, tekintse meg [a örökölt Exchange-társítás átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-exchange-powershell.md)című témakört.

### <a name="work-with-azure-powershell"></a>Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társ létrehozása és kiépítése

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Az Exchange-társítás támogatott társi helyeinek listájának beolvasása
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange-társ létrehozása
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Exchange-társítás beolvasása
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange-társak módosítása
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Exchange-társ kiépítése

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Közvetlen társrendszer létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)
* [Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

További információ: [internetes peering GYIK](faqs.md).
