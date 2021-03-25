---
title: Az útválasztási beállítások nem mérhetők az Azure-ban
description: Ismerje meg, hogyan konfigurálhatja az erőforrások útválasztási beállításait a CDN-szolgáltató egressing.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 00e0d1afa2aa035fe27e73dae60e291ea53bd6b8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105042849"
---
# <a name="what-is-routing-preference-unmetered"></a>Mi az útválasztási preferencia mérése?

A nem mért útválasztási preferencia Content Delivery Network (CDN) szolgáltatók számára érhető el, akik az Azure-ban üzemeltetett ügyfelekkel rendelkeznek. A szolgáltatás lehetővé teszi a CDN-szolgáltatók számára, hogy a különböző helyeken található Microsoft globális hálózati peremhálózati útválasztókkal közvetlen kapcsolatot hozzanak létre.

![Az útválasztási preferencia nem mért](media/routing-preference-unmetered/unmetered.png)

A hálózati forgalom az Azure-ból származó egressing származik, és a CDN-szolgáltató a közvetlen kapcsolat előnyeit élvezheti.
* A közvetlen kapcsolatokon keresztül átirányított Azure-erőforrások forgalmára vonatkozó adatforgalmi számla egressing ingyenes.
* A CDN-szolgáltató és az Azure-beli forrás közötti közvetlen kapcsolat optimális teljesítményt biztosít, mivel a között nincsenek ugrások. Ez előnyben részesül a CDN-munkaterhelésben, amely gyakran beolvassa az adatait a forrástól.

## <a name="configuring-routing-preference-unmetered"></a>Az útválasztási preferencia beállítása nem mért

Ahhoz, hogy a CDN-szolgáltatók kihasználják a nem mért útválasztási beállításokat, a CDN-szolgáltatóknak a program részét kell képezniük. Ha a CDN-szolgáltató nem része a programnak, forduljon a CDN-szolgáltatóhoz.

Ezután konfigurálja az erőforrásokra vonatkozó útválasztási beállításokat, és állítsa be az útválasztási preferencia típusát az **Internet** értékre. Konfigurálhatja az útválasztási beállításokat egy nyilvános IP-cím létrehozásakor, majd hozzárendelheti a nyilvános IP-címet olyan erőforrásokhoz, mint például a virtuális gépek, az internetkapcsolattal rendelkező terheléselosztó stb. [Megtudhatja, hogyan konfigurálhatja a nyilvános IP-címek útválasztási beállításait a Azure Portal használatával](routing-preference-portal.md)

Emellett engedélyezheti a Storage-fiók útválasztási beállításait, és közzétehet egy második végpontot, amelyet a CDN-szolgáltatónak kell használnia az adatok tárolási forrásból való lekéréséhez. Ha például egy internetes útválasztási végpontot tesz közzé a Storage-fiók *StorageAccountA* , a tárolási szolgáltatások második végpontját a lent látható módon fogja közzétenni:

![A Storage-fiókok útválasztási beállításai](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Következő lépések

* [Útválasztási beállítások konfigurálása virtuális géphez a Azure PowerShell használatával](configure-routing-preference-virtual-machine-powershell.md)
* [Útválasztási beállítások konfigurálása virtuális géphez az Azure CLI használatával](configure-routing-preference-virtual-machine-cli.md)
* [Útválasztási beállítások konfigurálása a Storage-fiókhoz](../storage/common/network-routing-preference.md)