---
title: A Azure NetApp Files teljesítményével kapcsolatos megfontolások | Microsoft Docs
description: A Azure NetApp Files teljesítményének megismerése, beleértve a kvóta-és átviteli sebességi korlátot, valamint a mennyiségi kvóták dinamikus növelését és csökkentését.
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
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713388"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítményével kapcsolatos szempontok

Az automatikus QoS-vel rendelkező kötetek [átviteli korlátját](azure-netapp-files-service-levels.md) a kötethez rendelt kvóta és a kiválasztott szolgáltatási szint kombinációja határozza meg. A manuális QoS-vel rendelkező kötetek esetében az átviteli korlát külön is definiálható. Ha Azure NetApp Filesra vonatkozó teljesítménnyel kapcsolatos terveket készít, meg kell ismernie néhány szempontot. 

## <a name="quota-and-throughput"></a>Kvóta és átviteli sebesség  

Az átviteli sebesség korlátja csak a tényleges teljesítmény egyik meghatározó tényezője.  

A tárolási teljesítmény jellemző szempontjai, beleértve az olvasási és írási kombinációt, az átvitel méretét, a véletlenszerű vagy szekvenciális mintákat, és számos más tényező is hozzájárul a teljes teljesítményhez.  

A tesztelés során megfigyelt maximális empirikus teljesítmény 4 500 MiB/s.  A Premium Storage szintjén a 70,31 TiB automatikus QoS mennyiségi kvótája kiépíti az adatátviteli korlátot, amely elég magas ahhoz, hogy a teljesítmény elérhető legyen.  

Automatikus QoS-kötetek esetén, ha a mennyiségi kvóta 70,31 TiB-nál nagyobb mennyiségének kiosztását fontolgatja, további kvóta is hozzárendelhető egy kötethez további adattároláshoz. A hozzáadott kvóta azonban nem eredményez további növekedést a tényleges átviteli sebességben.  

Az empirikus átviteli kapacitás felső határa a manuális QoS-vel rendelkező kötetekre vonatkozik. Egy kötethez hozzárendelhető maximális átviteli sebesség 4 500 MiB/s.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Automatikus QoS mennyiségi kvóta és átviteli sebesség

Ez a szakasz a kvóták kezelését és az automatikus QoS-típussal rendelkező kötetek átviteli sebességét ismerteti.

### <a name="overprovisioning-the-volume-quota"></a>A mennyiségi kvóta túlzott kiépítése

Ha a munkaterhelések teljesítménye meghaladja az átviteli sebességet, lehetséges, hogy az automatikus QoS mennyiségi kvótáját a magasabb átviteli sebesség beállítására és nagyobb teljesítményre van korlátozva.  

Ha például a Premium Storage-szinten lévő automatikus QoS-köteten csak 500 GiB adat van, de 128 MiB/s átviteli sebességre van szükség, beállíthatja a kvótát 2 TiB-128 ra úgy, hogy az átviteli szint megfelelően legyen beállítva (64 MiB/s).  

Ha következetesen túlépít egy kötetet a magasabb átviteli sebesség eléréséhez, érdemes lehet a manuális QoS-köteteket használni, vagy inkább magasabb szolgáltatási szintet használni.  A fenti példában ugyanezt az átviteli sebességet is elérheti, ha az automatikus QoS mennyiségi kvótát is használja az ultra Storage-rétegek használatával (128 MiB/s/TiB * 1 TiB = 128 MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dinamikusan növekvő vagy csökkenő mennyiségi kvóta

Ha a teljesítményre vonatkozó követelmények átmeneti jellegűek, vagy ha egy meghatározott időtartamnál nagyobb teljesítményre van szükség, dinamikusan növelheti vagy csökkentheti a mennyiségi kvótát, hogy azonnal módosítsa az átviteli sebességet.  Vegye figyelembe az alábbi szempontokat: 

* A mennyiségi kvóta az IO szüneteltetése nélkül növelhető vagy csökkenthető, és a kötethez való hozzáférés nem szakad meg, vagy nincs hatással rá.  

    A kvótát egy köteten lévő aktív I/O-tranzakció során is módosíthatja.  Vegye figyelembe, hogy a mennyiségi kvóta soha nem csökkenthető a köteten tárolt logikai adatmennyiség alatt.

* A mennyiségi kvóta megváltozásakor az átviteli sebesség korlátjának megfelelő módosítása majdnem azonnali. 

    A módosítás nem szakítja meg a kötet-vagy I/O-hozzáférést.  

* A mennyiségi kvóta módosítása szükségessé teheti a kapacitási készlet méretének változását.  

    A kapacitási készlet mérete dinamikusan módosítható, és nem befolyásolja a kötetek rendelkezésre állását vagy az I/O-t.

## <a name="manual-qos-volume-quota-and-throughput"></a>QoS mennyiségi kvótájának és átviteli sebességének manuális meghatározása 

Ha manuális QoS-köteteket használ, nem kell túlépítenie a mennyiségi kvótát a nagyobb átviteli sebesség eléréséhez, mert az átviteli sebességet egymástól függetlenül lehet hozzárendelni az egyes kötetekhez. Azonban továbbra is biztosítania kell, hogy a kapacitás-készlet előre kiépítve legyen a teljesítményre vonatkozó igényeknek megfelelő átviteli sebességgel. A kapacitás-készlet átviteli sebessége a méret és a szolgáltatási szint szerint van kiépítve. További részletekért tekintse [meg Azure NetApp Files szolgáltatási szintjeit](azure-netapp-files-service-levels.md) .


## <a name="next-steps"></a>Következő lépések

- [Teljesítmény-kalkulátor Azure NetApp Files](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- [Teljesítménytesztek Linuxhoz](performance-benchmarks-linux.md)
