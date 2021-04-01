---
title: Lemez típusának kiválasztása az Azure IaaS virtuális gépek által felügyelt lemezekhez
description: Ismerje meg a virtuális gépekhez elérhető Azure-lemezeket, például az ultra-lemezeket, a prémium SSD-ket, a standard SSD-ket és a standard HDD-ket.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1a7e8e71e26af241d16095a5fa1e6a02a7e3d4c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500767"
---
# <a name="what-disk-types-are-available-in-azure"></a>Milyen típusú lemezek érhetőek el az Azure-ban?

Az Azure Managed Disks jelenleg négy lemez típust kínál, amelyek mindegyike konkrét felhasználói forgatókönyvek elérésére szolgál.

## <a name="disk-comparison"></a>Lemezek összehasonlítása

Az alábbi táblázat az ultra Disks, a prémium szintű SSD-meghajtók (SSD), a standard SSD és a standard merevlemez-meghajtók (HDD) összehasonlítását ismerteti a felügyelt lemezekhez, amelyek segítségével eldöntheti, hogy mi a használat.

| Részletek | Ultralemez | Prémium SSD | Standard SSD | Standard HDD |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Lemeztípus   |SSD   |SSD   |SSD   |HDD   |
|Forgatókönyv   |I/o-igényes számítási feladatok, mint például a [SAP HANA](workloads/sap/hana-vm-operations-storage.md), a legfelső szintű adatbázisok (például SQL, Oracle) és az egyéb tranzakció-nagy számítási feladatok.   |Éles, teljesítményérzékeny számítási feladatok   |Webkiszolgálók, kisebb terhelésű vállalati alkalmazások és fejlesztés/tesztelés   |Biztonsági mentés, nem kritikus, ritka hozzáférés   |
|Lemez maximális mérete   |65 536 gibibájt (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maximális átviteli sebesség   |2 000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Maximális IOPS-érték   |160 000    |20 000   |6.000   |2000   |

## <a name="ultra-disk"></a>Ultralemez

Az Azure ultralemezei magas átviteli sebességet, magas IOPS-t, és konzisztensen alacsony késésű lemeztárhelyet biztosítanak Azure Iaas virtuális gépek számára. Az ultra-lemezek további előnyei közé tartozik a lemez teljesítményének dinamikus módosítása, valamint a számítási feladatok, anélkül, hogy újra kellene indítania a virtuális gépeket (VM). Az ultralemezek olyan adatigényes számítási feladatokhoz használhatók, mint az SAP HANA, a felső szintű adatbázisok és a tranzakcióigényes számítási feladatok. Az ultralemezek csak adatlemezként használhatók. Prémium SSD-lemezeket célszerű operációsrendszer-lemezként használnia.

### <a name="performance"></a>Teljesítmény

Ultra-lemez kiépítésekor egymástól függetlenül konfigurálhatja a lemez kapacitását és teljesítményét. Az ultra Disks számos rögzített méretben érhető el: 4 GiB-ig 64 TiB-ig, és egy rugalmas teljesítmény-konfigurációs modellt tartalmaz, amely lehetővé teszi a IOPS és az átviteli sebesség egymástól független konfigurálását.

Az ultra-lemezek főbb képességei a következők:

- Lemez kapacitása: az ultrakönnyű lemezek kapacitása 4 GiB-tól 64 TiB-ig terjed.
- Lemezes IOPS: az ultra Disks támogatja a 300 IOPS/GiB IOPS korlátait, legfeljebb 160 K IOPS. A kiépített IOPS eléréséhez győződjön meg arról, hogy a kiválasztott lemez IOPS kisebbek, mint a virtuális gép IOPS korlátja. A minimális garantált IOPS-érték 2 IOPS/GiB, a teljes alapkonfiguráció minimum 100 IOPS. Ha például 4 GiB Ultra-lemezzel rendelkezett, akkor nyolc IOPS helyett legalább 100 IOPS kell lennie.
- Lemez sebessége: az ultra Disks használatával az egyes kiépített IOPS 256 KiB/s-ra, a maximálisan 2000 MBps-ra vetítve (ahol MBps = 10 ^ 6 bájt/másodperc). Minden egyes kiépített IOPS esetében a minimális garantált átviteli sebesség 4KiB/s, a teljes alapértéknek legalább 1 MBps-nek kell lennie.
- Az ultra Disks támogatja a lemez teljesítmény-attribútumainak (IOPS és átviteli sebesség) beállítását futásidőben, anélkül, hogy leválasztja a lemezt a virtuális gépről. Ha a lemez teljesítményének átméretezési művelete egy lemezen van kiadva, akár egy óráig is eltarthat, amíg a változás ténylegesen érvénybe lép. Egy 24 órás időszakban legfeljebb négy teljesítmény-átméretezési művelet lehet. A teljesítmény-átméretezési művelet sikertelen a teljesítmény-sávszélességi kapacitás hiánya miatt.

### <a name="disk-size"></a>Lemezméret

|Lemez mérete (GiB)  |IOPS sapka  |Átviteli sebesség (MB/s)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2 400         |600         |
|16     |4800         |1200         |
|32     |9600         |2000         |
|64     |19 200         |2000         |
|128     |38 400         |2000         |
|256     |76 800         |2000         |
|512     |153 600         |2000         |
|1024 – 65536 (az ebben a tartományban lévő méretek 1 TiB-onként növekednek)     |160 000         |2000         |

Az ultra-lemezek úgy lettek kialakítva, hogy az idő 99,99%-ában az előző táblázatban ismertetett ezredmásodperces késéseket és célként megadott IOPS és átviteli sebességet biztosítson.

### <a name="ga-scope-and-limitations"></a>A GA hatóköre és korlátai

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Ha szeretné megkezdeni az ultra-lemezek használatát, tekintse meg a tárgyat ismertető cikket: az [Azure Ultra Disks használatával](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>Prémium SSD

Az Azure Premium SSD-k nagy teljesítményű és kis késleltetésű lemezes támogatást biztosítanak a virtuális gépek (VM) számára a bemeneti/kimeneti (i/o) igényű számítási feladatokhoz. A Premium Storage-lemezek sebességének és teljesítményének kihasználásához áttelepítheti a meglévő VM-lemezeket prémium SSD-re. A prémium SSD-k megfelelőek a kritikus fontosságú üzemi alkalmazások számára. A prémium SSD-k csak a Premium Storage-kompatibilis virtuálisgép-sorozatokkal használhatók.

Ha többet szeretne megtudni az Azure-ban Windows vagy Linux rendszerhez készült egyéni virtuálisgép-típusokról és-méretekről, beleértve a Premium Storage-kompatibilis méreteket, lásd: [virtuális gépek méretei az Azure-ban](sizes.md). Ebből a cikkből megtudhatja, hogy az egyes virtuálisgép-méretekről szóló cikkekben a Premium Storage-kompatibilis-e.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Ha Premium Storage-lemezt épít ki, a standard szintű tárterülettől eltérően, akkor garantált a lemez kapacitása, IOPS és átviteli sebessége. Ha például létrehoz egy P50 lemezt, az Azure 4 095 GB tárolókapacitást, 7 500 IOPS és 250 MB/s adatátviteli sebességet biztosít a lemez számára. Az alkalmazás a kapacitás és a teljesítmény egészét vagy egy részét használhatja. A prémium SSD lemezek úgy lettek kialakítva, hogy alacsony egyszámjegyű ezredmásodperces késéseket és célként megadott IOPS és átviteli sebességet biztosítson az idő 99,9%-ában.

## <a name="bursting"></a>Tele

A P30-nál kisebb méretű prémium SSD a lemezes adatmennyiséget, a IOPS pedig akár 3 500-ig, akár 170 MB/s-ra növelve. A bursás automatizált, és kreditrendszer alapján működik. A kreditek automatikusan halmozódnak, amikor a lemezes forgalom a kiosztott teljesítményű célpont alá esik, és a rendszer automatikusan felhasználja a krediteket, amikor a forgalom a célon kívül esik, a maximális burst korlátig. A maximális burst korlát határozza meg a lemez IOPS & sávszélességét akkor is, ha a rendszer felhasználja a feltört krediteket. A lemezek felskálázása nagyobb toleranciát biztosít az i/o-minták előre nem látható változásaihoz. A legjobb megoldás az operációs rendszer lemezes rendszerindításához és a tüskés forgalommal rendelkező alkalmazásokhoz.    

A lemezek kitörésének támogatását a rendszer alapértelmezés szerint engedélyezi a megfelelő méretű lemezekre vonatkozó új központi telepítések esetén, és nincs szükség felhasználói beavatkozásra. A megfelelő méretű lemezek meglévő lemezei esetében engedélyezheti a kitörést a két lehetőség közül: válassza le és csatlakoztassa újra a lemezt, vagy állítsa le, majd indítsa újra a csatlakoztatott virtuális gépet. Az összes érintett lemez mérete a teljes burst kredit gyűjtővel kezdődik, ha a lemez egy olyan virtuális géphez van csatlakoztatva, amely legfeljebb 30 percnél hosszabb időt támogat a maximálisan megengedettnél. Ha többet szeretne megtudni az Azure-lemezek kitörésével kapcsolatban, tekintse meg a [prémium SSD burst](./disk-bursting.md)című témakört. 

### <a name="transactions"></a>Tranzakciók

A prémium SSD-k esetében minden I/O-művelet a 256 KiB-as vagy annál nagyobb átviteli sebesség esetében egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak.

## <a name="standard-ssd"></a>Standard SSD

Az Azure standard SSD-k költséghatékony tárolási lehetőséget biztosítanak olyan számítási feladatokhoz, amelyeknek konzisztens teljesítményre van szükségük az alacsonyabb IOPS szinten. A standard SSD jó beléptetési szintű élményt nyújt azok számára, akik a felhőbe szeretnének költözni, különösen akkor, ha a helyszíni HDD-megoldásokon futó munkaterhelések ingadozásával kapcsolatos problémákat tapasztal. A standard merevlemezekhez képest a standard SSD-k jobb rendelkezésre állást, következetességet, megbízhatóságot és késést biztosítanak. A standard SSD-k a webkiszolgálók, az alacsony IOPS, a könnyű használatú vállalati alkalmazások és a fejlesztési/tesztelési feladatok számára alkalmasak. A standard merevlemezekhez hasonlóan a standard SSD-k is elérhetők az összes Azure-beli virtuális gépen.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

A standard SSD-k úgy lettek kialakítva, hogy egyszámjegyű ezredmásodperces késéseket, valamint a IOPS és az átviteli sebességet az előző táblázatban 99%-ban leírt korlátokig. A tényleges IOPS és átviteli sebesség esetenként eltérő lehet a forgalmi mintáktól függően. A standard SSD-k konzisztens teljesítményt biztosítanak, mint a HDD-lemezek, amelyek alacsonyabb késéssel rendelkeznek.

### <a name="transactions"></a>Tranzakciók

A standard SSD-k esetében minden I/O-művelet a 256 KiB-nál kisebb vagy azzal egyenlő, egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak. Ezek a tranzakciók számlázási hatással vannak.

## <a name="standard-hdd"></a>Standard HDD

Az Azure standard HDD-k megbízható, alacsony díjszabású lemezes támogatást biztosítanak a késést okozó munkaterheléseket futtató virtuális gépek számára. A standard szintű Storage szolgáltatásban az adattárakat merevlemez-meghajtókon (HDD-k) tárolja a rendszer. A standard HDD lemezek késése, IOPS és átviteli sebessége az SSD-alapú lemezekhez képest szélesebb körben is változhat. Standard HDD lemezeken írási késések érhetők el a 10ms alatt, a legtöbb IO-művelet esetében pedig a 20ms alatt olvashatók a késések, azonban a tényleges teljesítmény az i/o-mérettől és a számítási feladatoktől függően változhat. A virtuális gépek használatakor szabványos HDD-lemezeket használhat a fejlesztési és tesztelési forgatókönyvekhez, valamint a kevésbé fontos munkaterhelésekhez. A standard szintű HDD-k minden Azure-régióban elérhetők, és minden Azure-beli virtuális géppel használhatók.

### <a name="disk-size"></a>Lemezméret
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Tranzakciók

A standard HDD-k esetében minden egyes IO-művelet egyetlen tranzakciónak tekintendő, az I/O-mérettől függetlenül. Ezek a tranzakciók számlázási hatással vannak.

## <a name="billing"></a>Számlázás

A felügyelt lemezek használatakor az alábbi számlázási szempontokat kell figyelembe venni:

- Lemeztípus
- felügyelt lemez mérete
- Pillanatképek
- Kimenő adatforgalom
- Tranzakciók száma

**Felügyelt lemez mérete**: a felügyelt lemezek számlázása a kiosztott méret alapján történik. Az Azure leképezi a kiosztott méretet (felfelé kerekítve) a legközelebbi felajánlott lemez méretére. A felkínált lemezterület részleteiért tekintse meg az előző táblázatokat. Minden lemez egy támogatott kiosztott lemezre mutat, és ennek megfelelően történik a számlázás. Ha például egy 200 GiB-standard SSD kiépített, az a E15 (256 GiB) lemez méretére vonatkozó ajánlatot képezi le. A kiosztott lemezek számlázása óránként, a tárolási ajánlat havi díja alapján történik. Ha például kiépített egy E10 lemezt, és 20 óra elteltével törölte azt, akkor a E10-ajánlat díja 20 óra. Ez a lemezre írt tényleges adatok mennyiségétől függetlenül.

**Pillanatképek**: a pillanatképek számlázása a felhasznált méret alapján történik. Ha például létrehoz egy pillanatképet egy felügyelt lemezről, amely 64 GiB kiosztott kapacitással rendelkezik, és a tényleges felhasznált adatok mérete 10 GiB, a pillanatkép számlázása csak a 10 GiB felhasznált adatméretre történik.

A pillanatképekkel kapcsolatos további információkért tekintse meg a [felügyelt lemez – áttekintés](managed-disks-overview.md)című témakör a pillanatképek című szakaszát.

**Kimenő** adatforgalom: a [kimenő](https://azure.microsoft.com/pricing/details/bandwidth/) adatforgalom (az Azure-adatközpontok által kiváltott adatok) a sávszélesség-használatért kell fizetnie.

**Tranzakciók**: a standard szintű felügyelt lemezeken végrehajtott tranzakciók száma. A standard SSD-k esetében minden I/O-művelet a 256 KiB-nál kisebb vagy azzal egyenlő, egyetlen I/O-műveletnek számít. A 256 KiB-nál nagyobb teljesítményű i/O-műveletek a 256 KiB-os méretnél több I/O-műveletnek számítanak. A standard HDD-k esetében minden egyes IO-művelet egyetlen tranzakciónak tekintendő, az I/O-mérettől függetlenül.

A Managed Disks díjszabásáról, beleértve a tranzakciós költségeket is, tekintse meg a [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra Disk VM foglalási díja

Az Azure-beli virtuális gépek képesek jelezni, hogy kompatibilisek-e az ultra Disks szolgáltatással. Az ultra Disk-kompatibilis virtuális gépek dedikált sávszélesség-kapacitást foglalnak magukban a számítási virtuálisgép-példány és a blokk tárolási méretezési egység között a teljesítmény optimalizálása és a késés csökkentése érdekében. Ha ezt a képességet hozzáadja a virtuális géphez, a foglalási díjat csak akkor számítjuk fel, ha a virtuális gépen engedélyezte az ultra Disk képességet, anélkül, hogy az eszközhöz hozzá kellene csatlakoztatni. Ha Ultra lemez van csatlakoztatva az ultra Disk-kompatibilis virtuális géphez, ez a díj nem lesz alkalmazva. Ez a díj a virtuális gép által kiosztott vCPU. 

> [!Note]
> A [korlátozott alapszintű](constrained-vcpu.md)virtuálisgép-méretek esetében a foglalás díja a vCPU tényleges számától, és nem a korlátozott magoktól függ. Standard_E32-8s_v3 esetén a foglalás díja 32 mag lesz. 

Az ultra Disk díjszabásáról az [Azure Disks díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/managed-disks/) tájékozódhat.

### <a name="azure-disk-reservation"></a>Azure-lemezek foglalása

A lemezes foglalás az a lehetőség, hogy a lemezes tárterületet egy évig előre, kedvezményes áron vásárolja meg, ami csökkenti a teljes költségeket. A lemezes foglalások megvásárlásakor kiválaszthat egy adott lemezes SKU-t egy adott régióban, például a 10 P30 (1TiB) prémium SSD-ket az USA 2. keleti régiójában egy éves időszakra. A foglalási élmény a fenntartott virtuális gépek (VM) példányaihoz hasonló. A megtakarítások maximalizálása érdekében a virtuális gépek és a lemezek foglalása is megtehető. Az Azure-lemezek foglalása egyéves kötelezettségvállalási tervet biztosít a P30 (1TiB) prémium SSD-SKU-k számára a P80 (32 TiB) és az összes éles régióban. A fenntartott lemezek díjszabásával kapcsolatos további információkért lásd az [Azure Disks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Következő lépések

A kezdéshez tekintse meg [Managed Disks díjszabását](https://azure.microsoft.com/pricing/details/managed-disks/) .