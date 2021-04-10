---
title: Teljesítmény optimalizálása az Azure Lsv2-sorozatú virtuális gépeken
description: Útmutató a megoldás teljesítményének optimalizálásához a Lsv2 sorozatú virtuális gépeken Windows-példa használatával.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 8d38e3f58de0fc4cc1e963c18b002dc1e16852ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556009"
---
# <a name="optimize-performance-on-the-lsv2-series-windows-virtual-machines"></a>A Lsv2-sorozatú Windows-alapú virtuális gépek teljesítményének optimalizálása

A Lsv2 sorozatú virtuális gépek számos olyan munkaterhelést támogatnak, amelyek nagy I/O-és átviteli sebességet igényelnek a helyi tárterületen számos alkalmazás és iparág esetében.  A Lsv2 sorozat ideális a Big Database, az SQL, a NoSQL adatbázisok, az adattárházak és a nagy tranzakciós adatbázisok, például a Cassandra, a MongoDB, a Cloudera és a Redis számára.

A Lsv2 sorozatú Virtual Machines (VM-EK) kialakítása maximalizálja az AMD EPYC™ 7551 processzort, hogy a lehető legjobb teljesítményt nyújtsa a processzor, a memória, a NVMe-eszközök és a virtuális gépek között. A hardveres teljesítmény maximalizálása mellett a Lsv2 sorozatú virtuális gépek úgy vannak kialakítva, hogy a Windows-és Linux-operációs rendszerek igényeivel működjenek a hardver és a szoftver jobb teljesítményének növelése érdekében.

A szoftver és a hardver finomhangolása a [Windows Server 2019 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)optimalizált verzióját eredményezte, amely az Azure Marketplace-en 2018 jelent meg, amely támogatja a maximális teljesítményt a NVMe-eszközökön a Lsv2 sorozatú virtuális gépeken.

Ez a cikk tippeket és javaslatokat tartalmaz, amelyekkel biztosíthatja, hogy a munkaterhelések és az alkalmazások a virtuális gépek számára tervezett maximális teljesítményt érjenek el. Az ezen a lapon található információk folyamatosan frissülnek, mivel az Azure piactéren egyre több Lsv2 optimalizált rendszerkép kerül be.

## <a name="amd-eypc-chipset-architecture"></a>AMD EYPC™ lapkakészlet architektúrája

Az Lsv2 sorozatú virtuális gépek az AMD EYPC™ kiszolgálói processzorokat használják a Zen-architektúrán alapuló kiszolgálókon. Az AMD által fejlesztett Infinity Fabric (IF) a EYPC™ méretezhető összekötőként a NUMA-modellhez, amely a Die, a Package és a multi-Package kommunikációhoz használható. A QPI (Quick-Path Interconnect) és az UPI (Ultra-Path Interconnect) az Intel modern monolitikus-Die-processzorokkal való összevetésével az AMD sok NUMA-alapú kis testű architektúrája teljesítménybeli előnyökkel és kihívásokkal is járhat. A memória-sávszélesség és a késési korlátok tényleges hatása a futtatott számítási feladatok típusától függően változhat.

## <a name="tips-for-maximizing-performance"></a>Tippek a teljesítmény maximalizálásához

* A Lsv2 sorozatú virtuális gépeket kihasználó hardverek nyolc I/O üzenetsor (QP) s NVMe-eszközöket használnak. Minden NVMe-eszköz I/O-várólistája tulajdonképpen egy pár: egy küldési várólista és egy befejezési várólista. A NVMe illesztőprogram úgy van beállítva, hogy optimalizálja a nyolc I/O-QPs kihasználtságát, ha az I/O-t egy ciklikus időszeletelési ütemterv szerint terjeszti. A maximális teljesítmény érdekében minden eszközön nyolc feladatot futtasson az egyeztetéshez.

* Ne keverje a NVMe-felügyeleti parancsokat (például NVMe SMART info Query stb.) az aktív számítási feladatok során NVMe I/O-parancsokkal. A Lsv2 NVMe-eszközöket a Hyper-V NVMe Direct technológiája támogatja, amely a "lassú üzemmód" értékre vált, amikor bármilyen NVMe rendszergazdai parancs függőben van. Ha ez történik, a Lsv2-felhasználók drámai teljesítménybeli csökkenést láthatnak a NVMe I/O-teljesítményében.

* A Lsv2-felhasználók nem hivatkozhatnak a virtuális gépen belül az adatmeghajtók számára jelentett NUMA-információkra (mind a 0-ra), hogy kidöntsenek a NUMA-affinitást az alkalmazásaikban. A jobb teljesítmény érdekében ajánlott a számítási feladatok felosztása a processzorok között, ha lehetséges. 

* A Lsv2 VM NVMe-eszközhöz tartozó I/O-várólista-párok maximális támogatott üzenetsor-mélysége 1024 (vs. Amazon i3 QD 32 limit). A Lsv2-felhasználóknak a (szintetikus) teljesítménytesztek számítási feladatait a 1024-es vagy alacsonyabb várólista-mélységre kell korlátoznia a várólista teljes feltételeinek elindításához, ami csökkentheti a teljesítményt.

## <a name="utilizing-local-nvme-storage"></a>Helyi NVMe-tároló használata

Az 1,92 TB-os NVMe-lemez helyi tárterülete minden Lsv2 virtuális gépen elmúló. A virtuális gép sikeres újraindításakor a helyi NVMe-lemezen lévő összes információ megmarad. Ha a virtuális gépet újra üzembe helyezi, lefoglalják vagy törölték, az NVMe nem maradnak meg. Az adatvédelem nem szűnik meg, ha egy másik probléma miatt a virtuális gép vagy a rajta futó hardver nem Kifogástalan állapotba kerül. Ha ez történik, a régi gazdagépen lévő összes adat biztonságos törlésre kerül.

Olyan esetek is előfordulnak, amikor a virtuális gépet egy másik gazdagépre kell áthelyezni, például egy tervezett karbantartási művelet során. A tervezett karbantartási műveletek és néhány hardverhiba a [Scheduled Eventssal](scheduled-events.md)várható. A Scheduled Eventst kell használni az előre jelzett karbantartási és helyreállítási műveletek frissítésének megmaradása érdekében.

Abban az esetben, ha egy tervezett karbantartási esemény megköveteli, hogy a virtuális gép új, üres helyi lemezekkel rendelkező gazdagépen legyen létrehozva, akkor az adatokat újra kell szinkronizálni (a régi gazdagépen lévő összes adat biztonságos törléséhez). Ez azért fordul elő, mert a Lsv2-sorozatú virtuális gépek jelenleg nem támogatják a helyi NVMe-lemez élő áttelepítését.

A tervezett karbantartásnak két módja van.

### <a name="standard-vm-customer-controlled-maintenance"></a>Standard szintű, ügyfél által vezérelt karbantartás

- A virtuális gép egy 30 napos időszak alatt a frissített gazdagépre kerül.
- A Lsv2 helyi tárolási adatvesztést okoz, ezért ajánlott a biztonsági másolat készítése az esemény előtt.

### <a name="automatic-maintenance"></a>Automatikus karbantartás

- Akkor következik be, ha az ügyfél nem hajtja végre az ügyfél által vezérelt karbantartást, vagy vészhelyzeti eljárások, például egy biztonsági nulladik nap esetén.
- Az ügyféladatok megőrzésére szolgál, de a virtuális gépek kis kockázatú, hogy lefagynak vagy újraindulnak.
- A Lsv2 helyi tárolási adatvesztést okoz, ezért ajánlott a biztonsági másolat készítése az esemény előtt.

Bármely közelgő szolgáltatási esemény esetén az ellenőrzött karbantartási folyamattal kiválaszthatja a frissítéshez legalkalmasabb időpontot. Az esemény előtt biztonsági mentést készíthet az adatairól a Premium Storage-ban. A karbantartási esemény befejeződése után visszatérhet az adataihoz a frissített Lsv2 virtuális gépek helyi NVMe-tárolójába.

A helyi NVMe-lemezeken tárolt adatok kezelésére szolgáló forgatókönyvek a következők:

- A virtuális gép fut és kifogástalan állapotú.
- A virtuális gép a helyén (Ön vagy az Azure) újraindul.
- A virtuális gép szüneteltetve van (leállítva, de lefoglalás nélkül).
- A tervezett karbantartás-karbantartási műveletek többsége.

Az ügyfél védelme érdekében az adatok biztonságos törlését szolgáló forgatókönyvek a következők:

- A virtuális gépet újra üzembe helyezi, leállították vagy törölték (Ön).
- A virtuális gép nem Kifogástalan állapotba kerül, és hardveres probléma miatt egy másik csomópontra kell a szolgáltatást meggyógyítania.
- Kis mennyiségű tervezett karbantartási karbantartási művelet, amely megköveteli, hogy a virtuális gép egy másik gazdagépre legyen hozzárendelve a karbantartáshoz.

Ha többet szeretne megtudni a helyi tárolóban található adatbiztonsági mentés lehetőségeiről, tekintse meg [Az Azure IaaS-lemezek biztonsági mentésével és vész-helyreállításával foglalkozó](../backup-and-disaster-recovery-for-azure-iaas-disks.md)témakört.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

* **Hogyan megkezdeni a Lsv2-sorozatú virtuális gépek üzembe helyezését?**  
   Hasonlóan más virtuális géphez, a [portál](quick-create-portal.md), az [Azure CLI](quick-create-cli.md)vagy a [PowerShell](quick-create-powershell.md) használatával hozhat létre virtuális gépet.

* **Egyetlen NVMe lemezhiba miatt a gazdagépen lévő összes virtuális gép meghibásodik?**  
   Ha a hardver csomóponton lemezhiba észlelhető, a hardver hibás állapotban van. Ha ez történik, a csomóponton lévő összes virtuális gép automatikusan le lesz osztva, és egy kifogástalan állapotú csomópontra kerül. Az Lsv2 sorozatú virtuális gépek esetében ez azt jelenti, hogy az ügyfél hibás csomópontján lévő adatai is biztonságosan törlődnek, és az ügyfélnek újra létre kell hoznia az új csomóponton. Ahogy azt az élő áttelepítés az Lsv2-on való elérhetővé válása előtt megtörtént, a hibás csomóponton lévő adatai proaktív módon lesznek áthelyezve a virtuális gépekkel, mivel azokat egy másik csomópontra helyezzük át.

* **Szükség van-e a Windows Server 2012 vagy a Windows Server 2016 rendszerben a Windows rendszerhez tartozó lekérdezési beállítások megtételéhez?**  
   A NVMe lekérdezés csak az Azure-beli Windows Server 2019-es számítógépen érhető el.  

* **Válthatok vissza egy hagyományos megszakítási szolgáltatás rutin (ISR) modellre?**  
   A Lsv2 sorozatú virtuális gépek NVMe lekérdezésre vannak optimalizálva. A rendszer folyamatosan biztosít frissítéseket a lekérdezési teljesítmény javítása érdekében.

* **Módosíthatom a Windows Server 2019 lekérdezési beállításait?**  
   A lekérdezési beállítások nem állíthatók be a felhasználó számára.
   
## <a name="next-steps"></a>Következő lépések

* Tekintse meg az Azure [tárolási teljesítményére optimalizált összes virtuális gép](../sizes-storage.md) specifikációit
