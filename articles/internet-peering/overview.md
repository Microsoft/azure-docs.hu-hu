---
title: A Microsofttal való együttműködés beállítása
titleSuffix: Azure
description: A peering áttekintése
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 8f960e58d47963cbb239d498af52e7adfc77caa9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586866"
---
# <a name="internet-peering-overview"></a>Internetes peering – áttekintés

A társítás a Microsoft globális hálózata (AS8075) és a hálózat közötti kapcsolat a Microsoft online szolgáltatások és Microsoft Azure szolgáltatások internetes forgalmának cseréje céljából. A szolgáltatók vagy szolgáltatók kérhetik a Microsofttal való kapcsolódást a saját peremhálózati helyeink bármelyikén. Az egyes kérelmeket a Microsoft ellenőrzi, hogy az megfelel-e a társi házirendnek. Kétféle módon állíthat be Microsoft Network hálózatot:

* **Közvetlen társítás:**

    A Microsoft Edge és a hálózat között a Microsoft Network hálózata közötti közvetlen fizikai kapcsolatokon keresztül létesítettük a kapcsolatot. A BGP-munkamenetek az útválasztási házirend keretében és az előzetesen egyeztetett szerződés használatával konfigurálhatók a kapcsolatokon keresztül. Ez a PNI néven is ismert.

* **Exchange-társ:**

    Ez az internetes cserék (IX) standard nyilvános társ-létesítési kapcsolataira vonatkozik. A Microsoft hálózat és a hálózat közötti fizikai kapcsolatok a IX által működtetett switch hálón keresztül működnek. A BGP-munkamenetek a IX által biztosított IP-tárhely használatával konfigurálhatók.

## <a name="benefits-of-peering-with-microsoft"></a>A Microsofttal való együttműködés előnyei
* A Microsoft adatforgalmának a Microsofttal való összevonással történő továbbításával csökkentheti az átviteli költségeket.
* Növelje ügyfelei teljesítményét azáltal, hogy csökkenti a hálózati ugrásokat és a késést a Microsoft Edge Network hálózatra.
* A Microsoft által redundáns helyekkel való összevonással biztosíthatja az ügyfelek adatforgalmát a hálózat vagy az átviteli szolgáltató hálózatának meghibásodása esetén.
* Megtudhatja, hogyan használhatók a kapcsolati mérőszámok, és hogyan használhatók a hálózattal kapcsolatos hibák.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Az Azure használatának előnyei a peering beállításához

A Microsoft Azure PowerShell vagy portál használatával való összevonását kérheti. Az ily módon beállított társítás Azure-erőforrásként kezelhető, és a következő előnyöket biztosítja:
* Egyszerűsített és automatizálható lépések a Microsofttal való egyenrangúság beállításához és kezeléséhez.
* Gyorsan és egyszerűen megtekintheti és kezelheti az összes társát egy helyen.
* Az összes kapcsolat állapot-és sávszélesség-adatai nyomon követése.
* Használhatja ugyanazt az előfizetést az Azure-Cloud Services eléréséhez.

Ha már rendelkezik a Microsofttal létesített összevonásokkal, a rendszer **örökölt** társításként hivatkozik rá. Úgy is dönthet, hogy az ilyen típusú és az Azure-erőforrást is kezeli, hogy kihasználhassa a fenti előnyöket. A **következő lépések** című szakaszban található hivatkozásokra kattintva elküldheti az új kéréseket, vagy átalakíthatja az örökölt társ-hozzárendelést az Azure-erőforrásra.

## <a name="peering-policy"></a>Társviszony-létesítési szabályzat
A Microsoft szelektív, de általában nyitott társítási szabályzattal rendelkezik. A társak kiválasztása a teljesítmény, a képesség és a kölcsönös előnyök alapján történik, és bizonyos technikai, kereskedelmi és jogi követelmények vonatkoznak rájuk. Részletekért lásd: társítási [házirend](policy.md).

## <a name="faq"></a>GYIK
A peering szolgáltatással kapcsolatos gyakori kérdésekért lásd: [internetes társközi – gyakori](faqs.md)kérdések.

## <a name="next-steps"></a>Következő lépések

* Ha szeretne többet megtudni a Microsofttal való közvetlen együttműködés beállításához szükséges lépésekről, kövesse a [Direct peering](walkthrough-direct-all.md) bemutatót
* A Microsofttal való Exchange-társítás beállításával kapcsolatos lépésekért kövesse az [Exchange-partneri útmutató](walkthrough-exchange-all.md) lépéseit
* Ebben a dokumentumban az Azure egyéb lényeges [hálózat képességeivel](../networking/networking-overview.md) ismerkedhet meg.