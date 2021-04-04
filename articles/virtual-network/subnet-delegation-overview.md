---
title: Mi az az alhálózat-delegálás az Azure Virtual Networkben?
description: Az alhálózati delegálás ismertetése az Azure Virtual Networkben
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2020
ms.author: kumud
ms.openlocfilehash: 2801aa6b2e2b72df815b170200587c49ec0bae14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538992"
---
# <a name="what-is-subnet-delegation"></a>Mi az alhálózat-delegálás?

Az alhálózati delegálás lehetővé teszi, hogy egy adott alhálózatot jelöljön ki egy Ön által választott Azure Pásti szolgáltatáshoz, amelyet a virtuális hálózatba kell befecskendezni. Az alhálózati delegálás teljes hozzáférést biztosít az ügyfélnek az Azure-szolgáltatások virtuális hálózatokba való integrálásának kezeléséhez.

Amikor egy alhálózatot delegál egy Azure-szolgáltatáshoz, lehetővé teszi, hogy a szolgáltatás az alhálózat számára alapvető hálózati konfigurációs szabályokat hozzon létre, amelyek segítségével az Azure-szolgáltatás stabil módon működtetheti a példányait. Ennek eredményeképpen előfordulhat, hogy az Azure-szolgáltatás néhány előzetes vagy utólagos telepítési feltételt hoz létre, például:
- a szolgáltatás üzembe helyezése megosztott és dedikált alhálózaton.
- adja hozzá a szolgáltatáshoz a hálózati leképezési házirendek egy készletét, amely a szolgáltatás megfelelő működéséhez szükséges.

##  <a name="advantages-of-subnet-delegation"></a>Az alhálózati delegálás előnyei

Az alhálózatok adott szolgáltatásokra való delegálása a következő előnyöket nyújtja:

- segít kijelölni egy alhálózatot egy vagy több Azure-szolgáltatáshoz, és az alhálózatban lévő példányokat igény szerint kezeli. A virtuális hálózat tulajdonosa például megadhatja a következőt egy delegált alhálózathoz az erőforrások és a hozzáférés jobb kezelése érdekében az alábbiak szerint:
    - hálózati biztonsági csoportokkal rendelkező hálózati szűrési forgalmi szabályzatok.
    - a felhasználó által megadott útvonalakkal rendelkező útválasztási házirendek.
    - szolgáltatások integrációja a szolgáltatás végpontjai konfigurációval.
- segíti a szolgáltatások a virtuális hálózattal való jobb integrálását azáltal, hogy a hálózati leképezési házirendek formájában meghatározza az üzembe helyezések előfeltételeit. Ez biztosítja, hogy a befecskendezett szolgáltatás működésének befolyásolására alkalmas műveletek letiltható legyen.


## <a name="who-can-delegate"></a>Kik delegálhat?
Az alhálózati delegálás egy olyan gyakorlat, amelyet a virtuális hálózat tulajdonosainak el kell végezniük az adott Azure-szolgáltatás egyik alhálózatának kijelöléséhez. Az Azure szolgáltatás ezen az alhálózaton helyezi üzembe a példányokat az ügyfél munkaterhelései általi felhasználás céljából.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Az alhálózat delegálásának hatása az alhálózatra
Az egyes Azure-szolgáltatások saját üzemi modellt határoznak meg, ahol meghatározhatják, hogy milyen tulajdonságokat végeznek, vagy nem támogatják a delegált alhálózatokat a következő esetekben:
- megosztott alhálózat más Azure-szolgáltatásokkal vagy virtuálisgép-méretezési csoporttal ugyanabban az alhálózatban, vagy csak olyan dedikált alhálózatot támogat, amely kizárólag a szolgáltatás példányaival rendelkezik.
- támogatja az NSG társítást a delegált alhálózattal.
- a delegált alhálózathoz társított NSG is társítható bármely más alhálózathoz.
- engedélyezi az útválasztási tábla társítását a delegált alhálózattal.
- lehetővé teszi, hogy a delegált alhálózathoz társított útválasztási táblázat más alhálózatokhoz legyen társítva.
- a delegált alhálózatban lévő IP-címek minimális számát határozza meg.
- a delegált alhálózat IP-címének megadását a magánhálózati IP-címtartomány (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12) alapján határozza meg.
- azt diktálja, hogy az egyéni DNS-konfiguráció Azure DNS bejegyzést tartalmaz.
- a delegálás eltávolítását igényli az alhálózat vagy a virtuális hálózat törlése előtt.
- nem használható privát végponttal, ha az alhálózat delegálva van.

A befecskendezett szolgáltatások a következőképpen is hozzáadhatják saját házirendjeiket:
- **Biztonsági házirendek**: az adott szolgáltatás működéséhez szükséges biztonsági szabályok gyűjteménye.
- **Útvonal-házirendek**: az adott szolgáltatás működéséhez szükséges útvonalak gyűjteménye.

## <a name="what-subnet-delegation-does-not-do"></a>Az alhálózati delegálás nem

A delegált alhálózatba befecskendezett Azure-szolgáltatások továbbra is rendelkeznek a nem delegált alhálózatok számára elérhető alapszintű tulajdonságokkal, például a következőkkel:
-  Az Azure-szolgáltatások a példányokat ügyfél-alhálózatokra is beadhatják, de nem befolyásolhatják a meglévő munkaterheléseket.
-  A szolgáltatások által alkalmazott szabályzatok vagy útvonalak rugalmasak, és az ügyfél felülbírálhatja azokat.

## <a name="next-steps"></a>Következő lépések

- [Alhálózat delegálása](manage-subnet-delegation.md)
