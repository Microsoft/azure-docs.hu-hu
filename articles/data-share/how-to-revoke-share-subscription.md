---
title: Megosztási előfizetés visszavonása az Azure-beli adatmegosztásban
description: Megtudhatja, hogyan vonhatja vissza a megosztási előfizetést egy címzetttől az Azure-adatmegosztás használatával.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87511853"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Fogyasztó megosztás-előfizetésének visszavonása az Azure-beli adatmegosztásban

Ez a cikk azt ismerteti, hogyan vonhatja vissza egy vagy több felhasználó megosztási előfizetését az Azure-adatmegosztás használatával. Ez megakadályozza, hogy a fogyasztó további pillanatképeket indítson. Ha a fogyasztó még nem váltott ki pillanatképet, a megosztás-előfizetés visszavonása után soha nem kapják meg az adatgyűjtést. Ha korábban már elindítottak egy pillanatképet, a legfrissebb adatok a fiókjában maradnak.

## <a name="navigate-to-a-sent-data-share"></a>Navigáljon az elküldett adatmegosztáshoz

Az Azure-adatmegosztás területen navigáljon az elküldett megosztáshoz, és válassza az **előfizetés megosztása** lapot.

![Megosztás-előfizetés visszavonása](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Jelölje be azon címzettek jelölőnégyzetét, akiknek a megosztási előfizetéseit törölni szeretné, majd kattintson a **Visszavonás** gombra. A fogyasztó már nem kap frissítéseket az adatkezeléshez.

## <a name="next-steps"></a>Következő lépések
További információ az [adatmegosztások figyeléséről](how-to-monitor.md).