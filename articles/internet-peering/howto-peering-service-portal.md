---
title: Az Azure-alapú társ-kezelés engedélyezése közvetlenül a Azure Portal használatával
titleSuffix: Azure
description: Az Azure-alapú társ-kezelés engedélyezése közvetlenül a Azure Portal használatával
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: a52e6038b622c004dc0d133394cd4f53600b2935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84700042"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Az Azure-alapú társ-kezelés engedélyezése közvetlenül a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan engedélyezhető az Azure-alapú [társ](overview-peering-service.md) -kezelés a Azure Portal használatával történő közvetlen társításhoz.

Ha szeretné, az útmutatót a [PowerShell](howto-peering-service-powershell.md)használatával végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) .
* Válasszon közvetlen társítást az előfizetésben, amelyhez engedélyezni szeretné a társ-szolgáltatást. Ha még nem rendelkezik ilyennel, alakítsa át az örökölt közvetlen társítást, vagy hozzon létre egy új közvetlen társat:
    * Örökölt közvetlen társítás átalakításához kövesse a [örökölt közvetlen társ konvertálása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)című témakör utasításait.
    * Új közvetlen társítás létrehozásához kövesse a [közvetlen társítás létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)című témakör utasításait.

## <a name="enable-peering-service-on-a-direct-peering"></a>A Peering Service engedélyezése közvetlen társviszony-létesítés esetében

### <a name="view-direct-peering"></a><a name= get></a>Közvetlen társak megtekintése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>A közvetlen társítás engedélyezése a partneri szolgáltatás számára

Miután megnyitotta a közvetlen társítást az előző lépésben, engedélyezze azt a társ-szolgáltatás számára.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társas kapcsolatok módosítása

A kapcsolódási beállítások módosításához tekintse meg a [közvetlen társítás létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)"közvetlen társítás módosítása" című szakaszát.

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társ átalakítása Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

A gyakori kérdésekért tekintse meg a következő témakört: [peering Service – gyakori](service-faqs.md)kérdések.