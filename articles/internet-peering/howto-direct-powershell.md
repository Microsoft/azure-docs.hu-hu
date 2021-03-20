---
title: Közvetlen társrendszer létrehozása vagy módosítása a PowerShell használatával
titleSuffix: Azure
description: Közvetlen társrendszer létrehozása vagy módosítása a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be4bab10d369fcefe626bbe890acfff7fc7004b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89079064"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Közvetlen társrendszer létrehozása vagy módosítása a PowerShell használatával

Ez a cikk azt ismerteti, hogyan hozható létre Microsoft Direct-társ a PowerShell-parancsmagok és a Azure Resource Manager telepítési modell használatával. A cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, ezt az útmutatót az Azure [Portalon](howto-direct-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen](walkthrough-direct-all.md) társítási útmutatót.
* Ha már rendelkezik olyan közvetlen kapcsolódási kapcsolattal a Microsofttal, amely nem az Azure-erőforrásokra lett konvertálva, tekintse meg [a örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-direct-powershell.md)című témakört.

### <a name="work-with-azure-powershell"></a>Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társítás létrehozása és kiépítése

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>A támogatott peering-alapú telephelyek listájának beolvasása a közvetlen társításhoz
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Közvetlen társ létrehozása
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társítás ellenőrzése
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Közvetlen társítás módosítása
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Közvetlen társak kiépítése
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)
* [Örökölt Exchange-társ konvertálása Azure-erőforrásra a PowerShell használatával](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

További információ: [internetes peering GYIK](faqs.md).
