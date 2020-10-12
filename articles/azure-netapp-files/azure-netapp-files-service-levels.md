---
title: Azure NetApp Files szolgáltatási szintjei | Microsoft Docs
description: A Azure NetApp Files szolgáltatási szintjeinek teljesítménybeli teljesítményét ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: b-juche
ms.openlocfilehash: 1c64bd10b34b61797cb3bf3de0cd7d2aa819e795
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777130"
---
# <a name="service-levels-for-azure-netapp-files"></a>Az Azure NetApp Files szolgáltatásszintjei
A szolgáltatási szint a kapacitási készlet egyik attribútuma. A szolgáltatási szinteket a rendszer a kötethez hozzárendelt kvóta alapján határozza meg, és megkülönbözteti a kapacitások megengedett maximális átviteli sebességét.

## <a name="supported-service-levels"></a>Támogatott szolgáltatási szintek

A Azure NetApp Files három szolgáltatási szintet támogat: *Ultra*, *Premium*és *standard*. 

* <a name="Ultra"></a>Ultra Storage

    Az ultra Storage-szinten legfeljebb 128 MiB/s átviteli sebesség adható meg, amelyhez 1 TiB-kapacitás van kiépítve. 

* <a name="Premium"></a>Prémium szintű Storage

    A prémium szintű tárolási szint akár 64 MiB/s átviteli sebességet biztosít, 1 TiB kapacitással kiépítve. 

* <a name="Standard"></a>Standard szintű Storage

    A standard szintű tárolási szint legfeljebb 16 MiB/s adatátviteli sebességet biztosít, 1 TiB kapacitással kiépítve.

## <a name="throughput-limits"></a>Átviteli korlátok

A kötetek átviteli sebességének korlátját a következő tényezők kombinációja határozza meg:
* Azon kapacitási készlet szolgáltatási szintje, amelyhez a kötet tartozik
* A kötethez rendelt kvóta  
* A kapacitási készlet QoS-típusa (*automatikus* vagy *manuális*)  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Átviteli sebességre vonatkozó példák az automatikus QoS-kapacitás készletében lévő kötetekre

Az alábbi ábra az automatikus QoS-kapacitási készletben lévő kötetek átviteli sebességének korlátozási példáit mutatja be:

![Szolgáltatási szint illusztrációja](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* A fenti 1. példában egy, a prémium szintű tárterülettel rendelkező automatikus QoS-kapacitású készletből származó kötetet, amely 2 TiB-kvótát rendel hozzá, 128 MiB/s (2 TiB * 64 MiB/s) átviteli korlátot kap. Ez a forgatókönyv a kapacitási készlet méretétől vagy a tényleges mennyiségi felhasználástól függetlenül érvényes.

* A fenti 2. példában egy automatikus QoS-kapacitású készletből származó, a 100-es számú prémium szintű tárolási szinttel rendelkező kötethez 6,25 MiB/s (0,09765625 TiB * 64 MiB/s) átviteli korlát lesz hozzárendelve. Ez a forgatókönyv a kapacitási készlet méretétől vagy a tényleges mennyiségi felhasználástól függetlenül érvényes.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Átviteli sebességre vonatkozó példák a manuális QoS-kapacitás készletében lévő kötetekre 

Ha kézi QoS-kapacitású készletet használ, a kötet kapacitását és átviteli sebességét egymástól függetlenül is hozzárendelheti. Amikor manuálisan hoz létre kötetet egy manuális QoS-kapacitási készletben, megadhatja az átviteli sebesség (MiB/S) értékét. A manuális QoS-készlet köteteihez rendelt teljes átviteli sebesség a készlet méretétől és a szolgáltatási szinttől függ. A korlátot a (kapacitási készlet mérete a TiB x szolgáltatási szint átviteli sebessége/TiB-ben). Például az ultra Service-szinttel rendelkező 10 TiB-es kapacitású készlet teljes átviteli kapacitása 1280 MiB/s (10 TiB x 128 MiB/s/TiB) áll rendelkezésre a kötetek számára.

SAP HANA rendszer esetén ez a kapacitás-készlet a következő kötetek létrehozására használható. Az egyes kötetek az alkalmazás követelményeinek megfelelő egyéni méretet és átviteli sebességet biztosítanak:

* SAP HANA adatmennyiség: 4 TiB méretű, legfeljebb 704 MiB/s
* SAP HANA naplózási kötet: méret 0,5 TiB vagy akár 256 MiB/s
* SAP HANA megosztott kötet: 1 TiB méretű, legfeljebb 64 MiB/s
* SAP HANA biztonsági mentési kötet: méret 4,5 TiB vagy akár 256 MiB/s

Az alábbi ábra szemlélteti a SAP HANA kötetek forgatókönyveit:

![QoS-SAP HANA kötetek forgatókönyvei](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Következő lépések

- [Azure NetApp Files díjszabási oldala](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Az Azure NetApp Files költségmodellje](azure-netapp-files-cost-model.md) 
- [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Kötetek szolgáltatásszintjének dinamikus módosítása](dynamic-change-volume-service-level.md) 
- [Szolgáltatási szintű célkitűzések a régiók közötti replikáláshoz](cross-region-replication-introduction.md#service-level-objectives)
