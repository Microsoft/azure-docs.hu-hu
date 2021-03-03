---
title: Azure Storage-típusok SAP-alapú számítási feladatokhoz
description: Azure Storage-típusok tervezése SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/26/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 329e09221467c2602355e091876c95f305db3578
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673734"
---
# <a name="azure-storage-types-for-sap-workload"></a>Azure Storage-tárolótípusok SAP számítási feladathoz
Az Azure számos különböző tárolási típussal rendelkezik, amelyek nagy mértékben különböznek a képességek, a teljesítmény, a késés és az árak között. A tárolási típusok némelyike nem, vagy kizárólag SAP-forgatókönyvekhez használható. Míg számos Azure-beli tárolási típus jól használható vagy speciális SAP-munkaterhelési forgatókönyvekhez van optimalizálva. Különösen a SAP HANA esetében egyes Azure-beli tárolási típusok minősítést kaptak a SAP HANAval való használathoz. Ebben a dokumentumban a különböző típusú tárolásokat vesszük át, és leírjuk a képességeiket és a használhatóságát az SAP-munkaterhelésekkel és az SAP-összetevőkkel.

Megjegyzés a cikk során használt egységekről. A nyilvános Felhőbeli szállítók a ([gibibájtnak](https://en.wikipedia.org/wiki/Gibibyte)) vagy a TiB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) as size units) használatával lettek áthelyezve gigabájt vagy terabájt helyett. Ezért az összes Azure-dokumentáció és-Prizing ezeket az egységeket használja.  A dokumentum teljes egészében a MiB-, GiB-és TiB-egységek ezen méretére hivatkozunk. Lehetséges, hogy MB, GB és TB csomaggal kell megterveznie. Ezért ügyeljen arra, hogy a számítások néhány kisebb eltérése legyen, ha egy 400 MiB/s átviteli sebességre van szükség a 250 MiB/mp átviteli sebesség helyett.

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage rugalmasság

A standard HDD, a standard SSD, az Azure Premium Storage és az ultra Disk Microsoft Azure tárolása három különböző tárolási csomóponton tárolja az alap VHD-t (operációs rendszer) és a virtuális gép csatlakoztatott adatlemezeit, illetve a virtuális merevlemezeket. Feladatátvétel egy másik replikára, és egy új replika kivetése egy tárolási csomópont meghibásodása esetén transzparens. A redundancia eredményeként **nem** szükséges bármilyen tárolási redundancia-réteget használni több Azure-lemezen. Ezt a tényt helyi redundáns tárolónak (LRS) nevezzük. A LRS az Azure-ban az ilyen típusú tárolók esetében alapértelmezett. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) biztosítja a megfelelő redundanciát, hogy ugyanazokat a SLA-kat, mint a többi natív Azure Storage.

Több redundancia-módszer is rendelkezésre áll, amelyek mindegyike az Azure által kínált különböző tárolási típusokra érvényes [Azure Storage-replikációval](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) kapcsolatos cikkekben szerepel. 

Azt is vegye figyelembe, hogy a különböző Azure-beli tárolási típusok befolyásolják a virtuális gépek rendelkezésre állási SLA-ra vonatkozó SLA-kat [Virtual Machines esetén](https://azure.microsoft.com/support/legal/sla/virtual-machines).

### <a name="azure-managed-disks"></a>Azure Managed Disks

A Managed Disks egy Azure Resource Manager erőforrástípus, amely az Azure Storage-fiókokban tárolt virtuális merevlemezek helyett használható. A Managed Disks automatikusan igazodik a (z) [rendelkezésre állási csoport] [Virtual-Machines-Manage-elérhetősége] helyhez, amelyhez a virtuális gép csatlakoztatva van, és így növelheti a virtuális gép és a virtuális gépen futó szolgáltatások rendelkezésre állását. További információért olvassa el az [áttekintő cikket](../../managed-disks-overview.md).

A rugalmassággal kapcsolatos példa a felügyelt lemezek előnyeit mutatja be:

- Egy Azure-beli rendelkezésre állási csoportba helyezi üzembe a két adatbázis-kezelő virtuális gépet az SAP-rendszerhez 
- Ahogy az Azure üzembe helyezi a virtuális gépeket, az operációsrendszer-lemezképpel rendelkező lemez egy másik Storage-fürtbe kerül. Ezzel elkerülheti, hogy mindkét virtuális gép az egyetlen Azure Storage-fürt hibája miatt is hatással legyen
- Amikor az ezekhez a virtuális gépekhez hozzárendelt új felügyelt lemezeket hoz létre az adatbázis adatai és naplófájljai számára, a két virtuális gép új lemezeit külön tároló fürtökbe is telepíti a rendszer, így az első virtuális gép egyik lemeze sem osztja meg a Storage-fürtöket a második virtuális gép lemezével.

Felügyelt lemezek nélküli üzembe helyezés az ügyfél által definiált Storage-fiókokban, a lemez kiosztása tetszőleges, és nem ismeri azt a tényt, hogy a virtuális gépeket egy AvSet belül, rugalmassági célokra helyezik üzembe.

> [!NOTE]
> Ebből kifolyólag és számos egyéb, kizárólag felügyelt lemezeken keresztül elérhető fejlesztés esetén a virtuális gépek új, az Azure Block Storage-t használó, a lemezekhez (az összes Azure-tárolóhoz (kivéve Azure NetApp Files) használt telepítése szükséges az Azure Managed Disks használatához az alap VHD/operációsrendszer-lemezekhez, az SAP-adatbázis fájljait tartalmazó adatlemezekhez. Független attól, hogy a virtuális gépeket rendelkezésre állási csoporton keresztül helyezi-e üzembe Availability Zones vagy a készletek és zónáktól függetlenül. A biztonsági másolatok tárolásához használt lemezek nem feltétlenül szükségesek a felügyelt lemezekhez.

> [!NOTE]
> Az Azure Managed Disks csak helyi redundanciát (LRS) biztosít. 


## <a name="storage-scenarios-with-sap-workloads"></a>Tárolási forgatókönyvek SAP-munkaterhelésekkel
Az Azure-ban üzembe helyezett verem különböző összetevőiben megőrzött tárterület szükséges az SAP-munkaterheléshez. Ezek a forgatókönyvek legalább a következőhöz hasonlóak:

- A virtuális gép alapszintű VHD-je, amely az operációs rendszert és a lemezen telepített egyéb szoftvereket tartalmazza. Ez a lemez/VHD a virtuális gép gyökere. A rajta történt módosításokat meg kell őrizni. Tehát a következő alkalommal, amikor leállítja és újraindítja a virtuális gépet, a még meglévő módosítások is megtörténnek. Különösen azokban az esetekben, amikor az Azure üzembe helyezi a virtuális gépet egy másik gazdagépre, mint amit eredetileg futtatott
- Megőrzött adatlemezek. Ezek a lemezek olyan VHD-k, amelyek az alkalmazásadatok tárolásához vannak csatolva a alkalmazásban. Ez az alkalmazásadatok lehet az adatbázis, a biztonságimásolat-fájlok vagy a Szoftvertelepítés fájljainak adatfájlja és naplózása/visszaállítása. Minden olyan lemez, amely az operációs rendszert tároló alap virtuális merevlemezen túl van
- Azok a fájlmegosztás vagy megosztott lemezek, amelyek a globális átviteli könyvtárat tartalmazzák a NetWeaver vagy S/4HANA számára. A megosztások tartalmát több virtuális gépen futó szoftver használja, vagy a magas rendelkezésre állású feladatátvevő fürt forgatókönyvek létrehozásához használatos.
- Az/sapmnt könyvtár vagy közös fájlmegosztás az EDI-folyamatokhoz vagy hasonló. A megosztások tartalmát több virtuális gépen futó szoftver használja, vagy a magas rendelkezésre állású feladatátvevő fürt forgatókönyvek létrehozásához használatos.

A következő néhány szakaszban a különböző Azure Storage-típusok és az SAP-munkaterhelések használhatósága is megvitatva lesz, amely a fenti négy forgatókönyvre vonatkozik. A különböző Azure Storage-típusok használatának általános kategorizálását a cikk az [Azure-ban elérhető lemez-típusok](../../disks-types.md)leírását ismerteti. Az SAP-számítási feladatok különböző Azure Storage-típusai használatára vonatkozó javaslatok nem lesznek jelentős mértékben eltérőek.

Az SAP NetWeaver/Application Layer (S/4HANA) Azure Storage-típusaira vonatkozó támogatási korlátozásokért olvassa el a 2015553-es [SAP-támogatási Megjegyzés](https://launchpad.support.sap.com/#/notes/2015553) a SAP HANA Certified és a támogatott Azure Storage-típusok című cikket, [SAP HANA Azure-beli virtuális gépek tárolási konfigurációit](./hana-vm-operations-storage.md).

A különböző Azure Storage-típusok leírását ismertető szakaszban további hátteret talál az SAP által támogatott tárterülettel kapcsolatos korlátozásokkal és lehetőségekkel. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Tárolási javaslatok az SAP Storage-forgatókönyvekhez
A részletek megkezdése előtt bemutatjuk a dokumentum elején már meglévő összegzést és javaslatokat. Míg az Azure Storage adott típusaira vonatkozó részletek a dokumentum ezen szakaszát követik. A táblázatban szereplő SAP Storage-forgatókönyvekre vonatkozó tárolási javaslatok összefoglalása a következőhöz hasonló:

| Használati forgatókönyv | Standard HDD | Standard SSD | Prémium szintű Storage | Ultralemez | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Operációsrendszer-lemez | nem megfelelő |  korlátozott megfelelő (nem gyártható) | ajánlott | nem lehetséges | nem lehetséges |
| Globális átviteli könyvtár | nem támogatott | nem támogatott | ajánlott | ajánlott | ajánlott |
| /sapmnt | nem megfelelő | korlátozott megfelelő (nem gyártható) | ajánlott | ajánlott | ajánlott |
| Adatbázis-kezelői adatmennyiség SAP HANA M/Mv2 VM-család | nem támogatott | nem támogatott | ajánlott | ajánlott | ajánlott<sup>2</sup> |
| Adatbázis-kezelői naplózási kötet SAP HANA M/Mv2 VM-család | nem támogatott | nem támogatott | ajánlott<sup>1</sup> | ajánlott | ajánlott<sup>2</sup> | 
| Adatbázis-kezelő adatmennyiség SAP HANA Esv3/Edsv4 VM-család | nem támogatott | nem támogatott | ajánlott | ajánlott | ajánlott<sup>2</sup> |
| Adatbázis-kezelői naplózási kötet SAP HANA Esv3/Edsv4 VM-család | nem támogatott | nem támogatott | nem támogatott | ajánlott | ajánlott<sup>2</sup> | 
| Az adatbázis-kezelő adatkötete nem HANA | nem támogatott | korlátozott megfelelő (nem gyártható) | ajánlott | ajánlott | nem támogatott |
| Adatbázis-kezelői naplózási kötet nem HANA M/Mv2 VM-család | nem támogatott | korlátozott megfelelő (nem gyártható) | ajánlott<sup>1</sup> | ajánlott | nem támogatott |
| Adatbázis-kezelői naplózási kötet nem HANA nem M/Mv2 VM-család | nem támogatott | korlátozott megfelelő (nem gyártható) | akár közepes számítási feladatokhoz is alkalmas | ajánlott | nem támogatott |


<sup>1</sup> az [Azure Írásgyorsító](../../how-to-enable-write-accelerator.md) használata az M/Mv2 virtuálisgép-családokhoz a log/relog-kötetek <sup>2</sup> . ANF használatához a/Hana/Data és a/Hana/log is szükséges a ANF 

A különböző tárolási típusok listájából várható jellemzők:

| Használati forgatókönyv | Standard HDD | Standard SSD | Prémium szintű Storage | Ultralemez | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Átviteli sebesség/IOPS SLA | nem | nem | igen | igen | igen |
| Késések olvasása | magas | közepes és magas | alacsony | ezredmásodperc | ezredmásodperc |
| Késleltetési írások | magas | közepes és magas  | alacsony (sub-ezredmásodperc<sup>1</sup>) | ezredmásodperc | ezredmásodperc |
| HANA támogatott | nem | nem | igen<sup>1</sup> | igen | igen |
| Lemezes Pillanatképek lehetséges | igen | igen | igen | nem | igen |
| Lemezek kiosztása különböző tárolási fürtökön rendelkezésre állási csoportok használatakor | felügyelt lemezeken keresztül | felügyelt lemezeken keresztül | felügyelt lemezeken keresztül | a lemez típusa nem támogatott a rendelkezésre állási csoportokon keresztül üzembe helyezett virtuális gépek esetén | nem<sup>3</sup> |
| Igazított Availability Zones | igen | igen | igen | igen | a Microsoft részvétele szükséges |
| Zónabeli redundancia | nem felügyelt lemezekhez | nem felügyelt lemezekhez | nem felügyelt lemezekhez | nem | nem |
| Geo-redundancia | nem felügyelt lemezekhez | nem felügyelt lemezekhez | nem | nem | nem |


<sup>1</sup> az [Azure Írásgyorsító](../../how-to-enable-write-accelerator.md) használata az M/Mv2 virtuálisgép-családokhoz a log/ismétlési naplók köteteihez

<sup>2</sup> a költségek a kiépített IOPS és az átviteli sebességtől függenek

<sup>3</sup> a különböző ANF kapacitású készletek létrehozása nem garantálja a kapacitás-készletek különböző tárolási egységekre való telepítését.


> [!IMPORTANT]
> Ha Azure NetApp Files (ANF) használatával kevesebb, mint 1 ezredmásodperces I/O-késést szeretne elérni, a Microsofttal együttműködve gondoskodjon a virtuális gépek és az NFS-megosztások megfelelő elhelyezéséről a ANF alapján. Eddig nincs olyan mechanizmus, amely egy üzembe helyezett virtuális gép és a ANF-on üzemeltetett NFS-kötetek közötti automatikus közelséget biztosít. A különböző Azure-régiók különböző beállításai miatt a hozzáadott hálózati késés az 1 ezredmásodpercnél nagyobb I/O-késést eredményezhet, ha a virtuális gép és az NFS-megosztás nincs lefoglalva a közelségbe.


> [!IMPORTANT]
> A jelenleg nem kínált Azure blokk Storage-alapú felügyelt lemezek egyike sem rendelkezik a rendelkezésre álló vagy a Azure NetApp Files bármely régióbeli vagy földrajzi redundanciával. Ennek eredményeképpen meg kell győződnie arról, hogy a magas rendelkezésre állású és a vész-helyreállítási architektúrák nem támaszkodnak a felügyelt lemezekre, az NFS-re vagy az SMB-megosztásokra vonatkozó bármilyen típusú Azure-beli natív tárolási replikációra.


## <a name="azure-premium-storage"></a>Prémium szintű Azure Storage
Az Azure Premium SSD Storage szolgáltatás a következő céllal lett bevezetve:

* Alacsony I/O-késés
* SLA-kat a IOPS és az átviteli sebességhez
* Kisebb változékonyság az I/O-késésben

Az ilyen típusú tárolók az adatbázis-kezelői munkaterheléseket célozzák meg, az Azure Premium Storage esetében pedig a IOPS és az átviteli költséghatékonyságot igénylő tárolási forgalom nem az ilyen lemezeken tárolt tényleges adatmennyiség, hanem egy ilyen lemez méret kategóriája, a lemezen tárolt adatok mennyiségétől függetlenül. A Premium Storage-ban olyan lemezeket is létrehozhat, amelyek nem közvetlenül vannak leképezve a (z) [prémium SSD](../../disks-types.md#premium-ssd)cikkben látható méret kategóriákba. A jelen cikk következtetései a következők:

- A tároló tartományokba vannak rendezve. Például a 513 GiB tartományba tartozó, 1024 GiB kapacitású lemez ugyanazokat a képességeket és a havi költségeket használja
- A IOPS/GiB nem követi a lineáris adatmennyiséget a méret kategórián belül. A 32-nál kisebb méretű lemezek esetében a IOPS magasabb sebességű. A 32 GiB és a 1024 GiB közötti lemezek esetében a IOPS-sebesség az 4-5 IOPS/GiB között van. A 32 767 GiB-ig terjedő nagyobb lemezek esetén a IOPS sebessége 1 lesz
- A tároló I/O-átviteli sebessége nem lineáris a lemez kategóriájának méretével. Kisebb lemezek esetében, mint például a 65 GiB és a 128 GiB kapacitás közötti kategória, az átviteli sebesség körülbelül 780KB/GiB. Míg a nagy méretű lemezek, például egy 32 767 GiB-lemez esetében az átviteli sebesség a 28KB/GiB közelében van
- A IOPS és az adatátviteli SLA-kat nem lehet módosítani a lemez kapacitásának módosítása nélkül


Az SAP számítási funkciói mátrixa a következőhöz hasonlóan néz ki:

| Képesség| Megjegyzés| Megjegyzések/hivatkozások | 
| --- | --- | --- | 
| OPERÁCIÓSRENDSZER-alap VHD | alkalmas | minden rendszer |
| Adatlemez | alkalmas | minden rendszer – [kifejezetten SAP HANA](../../how-to-enable-write-accelerator.md) |
| SAP globális átviteli könyvtár | IGEN | [Támogatott](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | alkalmas | minden rendszer |
| Biztonsági mentési tár | alkalmas | a biztonsági másolatok rövid távú tárolásához |
| Megosztások/megosztott lemez | nem elérhető el | Az Azure Premium Files vagy harmadik fél számára szükséges |
| Rugalmasság | LRS | Nem érhető el GRS vagy ZRS lemezek számára |
| Késés | alacsony – közepes | - |
| IOPS SLA | IGEN | - |
| IOPS lineáris kapacitás | félig lineáris zárójelben  | [Felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS maximális száma lemezenként | 20 000 [a lemez méretétől függ](https://azure.microsoft.com/pricing/details/managed-disks/) | A [virtuális gépek korlátozásait](../../sizes.md) is figyelembe kell venni |
| Átviteli sebesség (SLA) | IGEN | - |
| Lineáris átviteli sebesség a kapacitásig | félig lineáris zárójelben | [Felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA Certified | IGEN | [kifejezetten SAP HANA](../../how-to-enable-write-accelerator.md) |
| Lemezes Pillanatképek lehetséges | IGEN | - |
| Azure Backup VM-Pillanatképek lehetséges | IGEN | [írásgyorsító](../../how-to-enable-write-accelerator.md) gyorsítótárazott lemezek kivételével  |
| Költségek | KÖZEPES | - |

Az Azure Premium Storage nem teljesíti SAP HANA tárolási késési KPI-ket az Azure Premium Storage-ban kínált közös gyorsítótárazási típusokkal. A tárolási késési KPI-k SAP HANA naplóbeli írások teljesítéséhez az Azure írásgyorsító gyorsítótárazást kell használnia az [Írásgyorsító engedélyezése](../../how-to-enable-write-accelerator.md)című cikkben leírtak szerint. Az Azure írásgyorsító a tranzakciónapló-írásokhoz és a naplók ismételt megismétléséhez szükséges összes egyéb adatbázis-kezelő rendszer előnyeit. Ezért azt javasoljuk, hogy az összes SAP adatbázis-kezelő üzemelő példányon keresztül használja. SAP HANA esetében az Azure-írásgyorsító használata az Azure Premium Storage szolgáltatással együtt kötelező.



**Összefoglalás:** Az Azure Premium Storage az SAP-munkaterhelésekhez ajánlott Azure Storage-típusok egyike. Ez a javaslat a nem éles környezetben, illetve a termelési rendszerekre is érvényes. Az Azure Premium Storage az adatbázis-munkaterhelések kezelésére alkalmas. Az Azure írásgyorsító használata jelentősen javítja az Azure Premium-lemezekkel kapcsolatos írási késleltetést. A nagy IOPS és átviteli sebességű adatbázis-kezelő rendszerek esetében azonban meg kell adnia a tárolókapacitást, vagy olyan funkciókat kell használnia, mint a Windows Storage Spaces vagy a logikai kötetek kezelői a Linuxon, hogy olyan csíkkészleteket hozzon létre, amelyek az egyik oldalon szeretnék a kívánt kapacitást használni, hanem a szükséges IOPS vagy átviteli sebességet is a legjobb költséghatékonyság érdekében.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure burst-funkciók a Premium Storage-hoz
Az Azure Premium Storage-lemezek esetében kisebb vagy egyenlő, mint a 512 GiB kapacitás, a burst funkció elérhető. A lemez-kitörés működésének pontos módját a [lemez kitörése](../../disk-bursting.md)című cikk ismerteti. A cikk elolvasása során megérti a IOPS és az átviteli sebesség felmerülésének koncepcióját abban az esetben, amikor az I/O-munkaterhelés a lemezek névleges IOPS és átviteli sebessége alá esik (a névleges átviteli sebességről lásd: [felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)). A IOPS és az átviteli sebesség különbözetét fogja felhalmozni a jelenlegi használat és a lemez névleges értékei között. A törések legfeljebb 30 percre vannak korlátozva.

Azok az ideális esetek, ahol a burst funkció megtervezhető, valószínűleg a különböző adatbázis-kezelő rendszer adatfájljait tartalmazó kötetek vagy lemezek lesznek. A várt I/O-számítási feladat várható a kötetek esetében, különösen kis-és közepes hatótávolságú rendszerek esetén a következőnek kell kinéznie:

- Kis-és közepes olvasási munkaterhelés, mivel az adatok ideális esetben a memóriában vannak gyorsítótárazva, vagy a HANA esetében a memóriában lévőnek kell lennie.
- Adatbázis-ellenőrzőpontok vagy visszaállítási pontok által kiváltott írások
- Olyan biztonsági mentési feladat, amely folyamatos streamet olvas be azokban az esetekben, amikor a biztonsági mentések nem a tárolási pillanatképeken keresztül futnak
- A SAP HANA a memóriába való betöltést egy példány újraindítása után

Különösen a kisebb adatbázis-kezelő rendszerek esetében, amelyekben a számítási feladatok csak néhány száz tranzakciót kezelnek, az ilyen adatfeldolgozási funkciók a tranzakciót tároló lemezek vagy kötetek számára is hasznosak lehetnek. Az ilyen lemezek vagy kötetek várható munkaterhelése a következőképpen néz ki:

- A munkaterheléstől és a számítási feladattól függő, rendszeres írás a lemezre, mivel az alkalmazás által kiadott összes véglegesített művelet valószínűleg I/O-műveletet indít el
- Nagyobb számítási feladatok az átviteli sebességben az üzemeltetési feladatok esetében, például indexek létrehozása vagy újraépítése
- Adatsorozatok beolvasása tranzakciónaplók végrehajtásakor vagy a naplók biztonsági másolatainak visszaállításakor


## <a name="azure-ultra-disk"></a>Azure Ultra Disk
Az Azure ultralemezei magas átviteli sebességet, magas IOPS-t, és konzisztensen alacsony késésű lemeztárhelyet biztosítanak Azure Iaas virtuális gépek számára. Az ultra-lemezek további előnyei közé tartozik a lemez IOPS és átviteli sebességének dinamikus módosítása, a számítási feladatokkal együtt, anélkül, hogy újra kellene indítania a virtuális gépeket (VM). Az ultra-lemezek olyan adatigényes számítási feladatokhoz használhatók, mint például az SAP adatbázis-kezelő munkaterhelése. Az ultra-lemezek csak adatlemezként használhatók, és nem használhatók az operációs rendszert tároló alap VHD-lemezként. Javasoljuk, hogy az Azure Premium Storage-t alapul szolgáló VHD-lemezként használják.

Ultra-lemez létrehozásakor három dimenziót adhat meg:

- A lemez kapacitása. A tartományok 4 GiB és 65 536 GiB között vannak.
- A lemez kiépített IOPS. A lemez kapacitására eltérő maximális értékek vonatkoznak. További részletekért olvassa el az [Ultra Disk](../../disks-types.md#ultra-disk) című cikket.
- Kiépített tárolási sávszélesség. A lemez kapacitása függ a maximális sávszélességtől. További részletekért olvassa el az [Ultra Disk](../../disks-types.md#ultra-disk) című cikket.

Az egyetlen lemez díját az adott lemezekhez külön definiált három dimenzió határozza meg. 


Az SAP számítási funkciói mátrixa a következőhöz hasonlóan néz ki:

| Képesség| Megjegyzés| Megjegyzések/hivatkozások | 
| --- | --- | --- | 
| OPERÁCIÓSRENDSZER-alap VHD | nem működik | - |
| Adatlemez | alkalmas | minden rendszer  |
| SAP globális átviteli könyvtár | IGEN | [Támogatott](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | alkalmas | minden rendszer |
| Biztonsági mentési tár | alkalmas | a biztonsági másolatok rövid távú tárolásához |
| Megosztások/megosztott lemez | nem elérhető el | Harmadik félnek kell lennie |
| Rugalmasság | LRS | Nem érhető el GRS vagy ZRS lemezek számára |
| Késés | nagyon alacsony | - |
| IOPS SLA | IGEN | - |
| IOPS lineáris kapacitás | félig lineáris zárójelben  | [Felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/) |
| IOPS maximális száma lemezenként | 1 200 – 160 000 | a lemez kapacitása függ |
| Átviteli sebesség (SLA) | IGEN | - |
| Lineáris átviteli sebesség a kapacitásig | félig lineáris zárójelben | [Felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA Certified | IGEN | - |
| Lemezes Pillanatképek lehetséges | NO | - |
| Azure Backup VM-Pillanatképek lehetséges | NO | - |
| Költségek | Magasabb, mint a Premium Storage | - |



**Összefoglalás:** Az Azure Ultra Disks egy megfelelő tárhely, amely alacsony késéssel jár az SAP-munkaterhelések összes típusához. Eddig az ultra Disk csak olyan virtuális gépek kombinációjában használható, amelyek Availability Zones (a zóna szerinti üzembe helyezéssel) üzembe helyezhetők. Az ultra Disk jelenleg nem támogatja a tárolási pillanatképeket ebben az időpontban. Az összes többi tárolóval szemben az ultra Disk nem használható az alapszintű VHD-lemezhez. Az ultra Disk ideális olyan esetekben, amikor az I/O-munkaterhelések nagy mértékben ingadoznak, és a sávszélesség és a IOPS maximális kihasználtsága helyett az üzembe helyezett tárterület átviteli sebességét vagy IOPS szeretné igazítani a tárolási munkaterhelés-mintákhoz.


## <a name="azure-netapp-files-anf"></a>Azure NetApp-fájlok (ANF)
A [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) a Microsoft és a NetApp együttműködésének eredményeként a nagy teljesítményű Azure natív NFS-és SMB-megosztások biztosításának céljával. A hangsúly arra szolgál, hogy magas sávszélességet és kis késésű tárolást biztosítson, amely lehetővé teszi az adatbázis-kezelő üzembe helyezési forgatókönyvek használatát, és idővel lehetővé teszi a NetApp-tároló tipikus működési funkcióinak használatát az Azure Az NFS-/SMB-megosztások három különböző szolgáltatási szinten érhetők el, amelyek megkülönböztetik a tárolási sebességet és az árat. A szolgáltatási szinteket a [Azure NetApp Files szolgáltatási szintjei](../../../azure-netapp-files/azure-netapp-files-service-levels.md)című cikk ismerteti. A különböző típusú SAP-számítási feladatok esetében a következő szolgáltatási szintek ajánlottak:

- SAP adatbázis-kezelő számítási feladatok: teljesítmény, ideális esetben Ultra
- SAPMNT-megosztás: teljesítmény, ideális esetben Ultra
- Globális átviteli könyvtár: teljesítmény, ideális esetben Ultra

> [!NOTE]
> A minimális kiépítési méret egy kapacitási készletnek nevezett 4 TiB-egység. Ezután köteteket hozhat létre ebből a kapacitási készletből. Míg a felépíthető legkisebb kötet 100 GiB. A kapacitási készlet bővíthető a TiB-lépésekben. A díjszabásról a [Azure NetApp Files díjszabását](https://azure.microsoft.com/pricing/details/netapp/) ismertető cikkben tájékozódhat.

A ANF-tároló jelenleg számos SAP-munkaterhelési forgatókönyv esetén támogatott:

- SMB-vagy NFS-megosztások biztosítása az SAP globális átviteli címtárához
- A megosztás sapmnt magas rendelkezésre állású forgatókönyvekben, a következőkben leírtak szerint:
    - [Magas rendelkezésre állás a Windows rendszerű Azure-beli virtuális gépeken futó SAP NetWeaver számára az SAP-alkalmazások Azure NetApp Files (SMB) szolgáltatásával](./high-availability-guide-windows-netapp-files-smb.md)
    - [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez SUSE Linux Enterprise Serveron Azure NetApp Files SAP-alkalmazásokhoz](./high-availability-guide-suse-netapp-files.md)
    - [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux SAP-alkalmazásokhoz Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA az NFS v 4.1-es verzióit használó központi telepítések a/Hana/Data-és/Hana/log-kötetek és/vagy az NFS v 4.1-es vagy NFS v3-kötetek számára a/Hana/Shared-kötetek esetében, az Azure-beli [virtuális gépek tárolási konfigurációjának SAP HANA](./hana-vm-operations-storage.md) cikkben

> [!NOTE]
> A Azure NetApp Files-alapú NFS-vagy SMB-megosztások nem támogatják az adatbázis-kezelő egyéb számítási feladatokat. A frissítések és a módosítások akkor lesznek megadva, ha ez megváltozik.

Az Azure Premium Storage szolgáltatáshoz hasonlóan a GB-onként rögzített vagy lineáris átviteli sebesség is problémát jelenthet, ha be kell tartania az átviteli sebesség minimális számát. Ehhez hasonlóan a SAP HANA esetében is. A ANF ezt a problémát a prémium szintű Azure-előfizetések részévé teheti. Ha a prémium szintű Azure-lemezre van szüksége, több kisebb lemezt is igénybe vehet, amelyeken a csomagok viszonylag magas átviteli sebességgel működnek, és az egészük költséghatékony, és nagyobb a kapacitásuk. Ez a fajta csíkozás nem működik az ANF-on üzemeltetett NFS-vagy SMB-megosztások esetén. Ez a korlátozás a többletkapacitás központi telepítését eredményezte, például:

- Ahhoz például, hogy a ANF-on üzemeltetett NFS-köteten 250 MiB/s átviteli sebesség legyen elérhető, az ultra Service-szint 1,95 TiB-kapacitását kell telepítenie. 
- A 400 MiB/mp eléréséhez telepítenie kell az 3,125 TiB-kapacitást. Előfordulhat azonban, hogy a kapacitás túlzott kiosztása szükséges a kötethez szükséges átviteli sebesség eléréséhez. A kapacitás túlzott kiépítése hatással van a kisebb HANA-példányok díjszabására. 
- Ha az NFS-t az SAP/sapmnt-címtár ANF-re kívánja használni, általában az 100 GiB minimális kapacitása lesz a 150 GiB, amelyet a Azure NetApp Files érvényesít. A felhasználói élmény azonban azt mutatta, hogy az 12,8 MiB/s (Ultra szolgáltatási szinttel rendelkező) kapcsolódó átviteli sebessége nem lehet elég, és negatív hatással lehet az SAP-rendszer stabilitására. Ilyen esetekben előfordulhat, hogy az ügyfelek a/sapmnt mennyiségének növelésével elkerülhetik a problémákat, így a kötethez több átviteli sebesség is biztosítható.

Az SAP számítási funkciói mátrixa a következőhöz hasonlóan néz ki:

| Képesség| Megjegyzés| Megjegyzések/hivatkozások | 
| --- | --- | --- | 
| OPERÁCIÓSRENDSZER-alap VHD | nem működik | - |
| Adatlemez | alkalmas | Csak SAP HANA  |
| SAP globális átviteli könyvtár | IGEN | SMB és NFS |
| SAP-sapmnt | alkalmas | minden rendszer SMB (csak Windows rendszeren) vagy NFS (csak Linux) |
| Biztonsági mentési tár | alkalmas | - |
| Megosztások/megosztott lemez | IGEN | SMB 3,0, NFS v3 és NFS v 4.1 |
| Rugalmasság | LRS | Nem érhető el GRS vagy ZRS lemezek számára |
| Késés | nagyon alacsony | - |
| IOPS SLA | IGEN | - |
| IOPS lineáris kapacitás | szigorúan lineáris  | [Szolgáltatási szinttől](../../../azure-netapp-files/azure-netapp-files-service-levels.md) függ |
| Átviteli sebesség (SLA) | IGEN | - |
| Lineáris átviteli sebesség a kapacitásig | félig lineáris zárójelben | [Szolgáltatási szinttől](../../../azure-netapp-files/azure-netapp-files-service-levels.md) függ |
| HANA Certified | IGEN | - |
| Lemezes Pillanatképek lehetséges | IGEN | - |
| Azure Backup VM-Pillanatképek lehetséges | NO | - |
| Költségek | Magasabb, mint a Premium Storage | - |


A ANF-tároló további beépített funkciója:

- Képesség a kötetek pillanatképének elvégzésére
- ANF-kötetek klónozása pillanatképekről
- Kötetek visszaállítása pillanatképekről (snap-REVERT)

**Összefoglalás**: Azure NETAPP Files egy HANA Certified kis késésű tároló, amely lehetővé teszi az NFS-és SMB-kötetek vagy-megosztások üzembe helyezését. A tároló három különböző szolgáltatási szintet tartalmaz, amelyek különböző átviteli sebességet és IOPS biztosítanak lineáris módon a köteten. A ANF-tároló lehetővé teszi SAP HANA kibővíthető forgatókönyvek üzembe helyezését készenléti csomóponttal. A tárterület a/sapmnt vagy az SAP globális átviteli címtár számára szükséges fájlmegosztás biztosítására alkalmas. A ANF-tároló a natív NetApp-funkcióként elérhető funkcionalitási rendelkezésre állást biztosít.  



## <a name="azure-standard-ssd-storage"></a>Azure standard SSD-tároló
Az Azure standard HDD-tárolóhoz képest az Azure standard SSD Storage jobb rendelkezésre állást, következetességet, megbízhatóságot és késést biztosít. Olyan számítási feladatokhoz van optimalizálva, amelyeknek az alacsonyabb IOPS szinten kell konzisztens teljesítményre van szükségük. Ez a tárterület az alacsony IOPS és átviteli sebességű, nem éles üzemi SAP-rendszerekhez használt minimális tárterület. Az SAP számítási funkciói mátrixa a következőhöz hasonlóan néz ki:

| Képesség| Megjegyzés| Megjegyzések/hivatkozások | 
| --- | --- | --- | 
| OPERÁCIÓSRENDSZER-alap VHD | korlátozott megfelelő | nem éles rendszerek |
| Adatlemez | korlátozott megfelelő | egyes nem éles rendszerek alacsony IOPS és késési igényekkel |
| SAP globális átviteli könyvtár | NO | [Nem támogatott](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | korlátozott megfelelő | nem éles rendszerek |
| Biztonsági mentési tár | alkalmas | - |
| Megosztások/megosztott lemez | nem elérhető el | Harmadik félnek kell lennie |
| Rugalmasság | LRS, GRS | Nincs elérhető ZRS a lemezekhez |
| Késés | magas | túl magas az SAP globális átviteli címtára vagy a termelési rendszerek esetében |
| IOPS SLA | NO | - |
| IOPS maximális száma lemezenként | 500 | A lemez méretétől függetlenül |
| Átviteli sebesség (SLA) | NO | - |
| HANA Certified | NO | - |
| Lemezes Pillanatképek lehetséges | IGEN | - |
| Azure Backup VM-Pillanatképek lehetséges | IGEN | - |
| Költségek | ALACSONY | - |



**Összefoglalás:** Az Azure standard SSD-tároló a nem éles környezetben üzemelő virtuális gépekre vonatkozó minimális javaslat, amely az alapszintű VHD-k, a végleges adatbázis-kezelői telepítések relatív késéssel és/vagy alacsony IOPS és átviteli sebességgel. Ez az Azure Storage-típus már nem támogatott az SAP globális átviteli címtárának üzemeltetéséhez. 



## <a name="azure-standard-hdd-storage"></a>Standard szintű Azure HDD-tároló
Az Azure standard HDD Storage szolgáltatás az egyetlen tárolási típus volt, amikor az Azure-infrastruktúra a 2014-es évben hitelesítette az SAP NetWeaver számítási feladatait. Az 2014-as évben az Azure-beli virtuális gépek kicsik és alacsonyak voltak a tárolási teljesítményben. Ezért ez a tárolási típus csak az igények kielégítésével tudott lépést tartani. A tárterület ideális a késésre érzékeny számítási feladatokhoz, így az SAP-térben alig tapasztalható. Az Azure-beli virtuális gépek növekvő átviteli sebessége és a virtuális gépek megnövekedett munkaterhelése miatt ezt a tárolási típust nem veszi figyelembe az SAP-forgatókönyvekkel való használat. Az SAP számítási funkciói mátrixa a következőhöz hasonlóan néz ki:

| Képesség| Megjegyzés| Megjegyzések/hivatkozások | 
| --- | --- | --- | 
| OPERÁCIÓSRENDSZER-alap VHD | nem megfelelő | - |
| Adatlemez | nem megfelelő | - |
| SAP globális átviteli könyvtár | NO | [Nem támogatott](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | NO | Nem támogatott |
| Biztonsági mentési tár | alkalmas | - |
| Megosztások/megosztott lemez | nem elérhető el | Szükséges Azure Files vagy harmadik fél |
| Rugalmasság | LRS, GRS | Nincs elérhető ZRS a lemezekhez |
| Késés | magas | túl magas az adatbázis-kezelői használat, az SAP globális átviteli könyvtára vagy a sapmnt/saploc |
| IOPS SLA | NO | - |
| IOPS maximális száma lemezenként | 500 | A lemez méretétől függetlenül |
| Átviteli sebesség (SLA) | NO | - |
| HANA Certified | NO | - |
| Lemezes Pillanatképek lehetséges | IGEN | - |
| Azure Backup VM-Pillanatképek lehetséges | IGEN | - |
| Költségek | ALACSONY | - |



**Összefoglalás:** Standard HDD egy olyan Azure Storage-típus, amelyet csak az SAP biztonsági mentések tárolására kell használni. A rendszer csak az olyan inaktív rendszerek alapszintű VHD-ként használható, mint például az adatok kereséséhez használt kivont rendszerek itt és ott. Az aktív fejlesztés, a MINŐSÉGBIZTOSÍTÁSi vagy a termelési virtuális gépek azonban nem alapulnak a tárolón. A tárolóban tárolt adatbázisfájlok nem


## <a name="azure-vm-limits-in-storage-traffic"></a>Azure-beli virtuális gépek korlátai a tárolási forgalomban
A helyi forgatókönyvekhez képest a kiválasztott virtuálisgép-típus az Ön által kiválasztott virtuális gép létfontosságú szerepet játszik az elérni kívánt tárolási sávszélességben. A különböző tárolási típusok esetében a következőket kell figyelembe vennie:

| Tárolási típus| Linux | Windows | Megjegyzések |
| --- | --- | --- | --- |
| Standard HDD | [A Linux rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | Valószínűleg nehéz megérinteni a közepes vagy nagyméretű virtuális gépek tárolási korlátait |
| Standard SSD | [A Linux rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | Valószínűleg nehéz megérinteni a közepes vagy nagyméretű virtuális gépek tárolási korlátait |
| Prémium szintű Storage | [A Linux rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | Könnyen elérheti a IOPS vagy a tárolási sebességű virtuális gépek korlátait a tárolási konfigurációval |
| Ultravékony lemezes tárolás | [A Linux rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | Könnyen elérheti a IOPS vagy a tárolási sebességű virtuális gépek korlátait a tárolási konfigurációval |
| Azure NetApp Files | [A Linux rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | [A Windows rendszerű virtuális gépek méretei az Azure-ban](../../sizes.md) | A tárolási forgalom hálózati átviteli sávszélességet és nem tárolási sávszélességet használ. |

Korlátozásként vegye figyelembe a következőket:

- Minél kisebb a virtuális gép, annál kevesebb lemezt lehet csatlakoztatni. Ez nem vonatkozik a ANF. Az NFS-vagy SMB-megosztások csatlakoztatása óta nem tapasztalható a csatolni kívánt megosztott kötetek számának korlátja.
- A virtuális gépek I/O-átviteli sebessége és a IOPS korlátai könnyen meghaladják a Premium Storage-lemezeket és az ultra-lemezeket
- A ANF esetében a megosztott kötetekre irányuló forgalom a virtuális gép hálózati sávszélességét és nem a tárolási sávszélességet vesz igénybe
- Ha nagy méretű NFS-köteteket használ a kétszámjegyű TiB kapacitású térben, akkor az ilyen kötethez hozzáférő átviteli sebesség egy virtuális gépen keresztül fog megjelenni a fennsíkon, a megosztott kötettel kommunikáló egyetlen munkamenet esetében pedig a Linux korlátain alapul. 

Ahogy az Azure-beli virtuális gépeket egy SAP-rendszer életciklusa során felhasználja, értékelnie kell a IOPS és a tárterület átviteli sebességének korlátait az új és nagyobb virtuálisgép-típus esetében. Bizonyos esetekben érdemes lehet módosítani a tárolási konfigurációt az Azure-beli virtuális gép új képességeire is. 


## <a name="striping-or-not-striping"></a>Csíkozás vagy nem csíkozás
Több Azure-lemezről egy nagyobb kötetre állított sáv létrehozása lehetővé teszi az egyes lemezek IOPS és átviteli sebességének összegyűjtését egyetlen kötetre. A szolgáltatás csak az Azure standard Storage és az Azure Premium Storage esetében használatos. Az Azure Ultra Disk, ahol az átviteli sebességet és a IOPS a lemez kapacitásatól függetlenül konfigurálhatja, a nem igényli a Stripe-készletek használatát. Az NFS-en vagy az SMB-en alapuló megosztott kötetek nem csíkozottak. Az Azure Premium Storage átviteli sebességének és IOPS nem lineáris jellege miatt kisebb kapacitást építhet ki ugyanazzal a IOPS és átviteli sebességgel, mint a nagyméretű, egyetlen Azure Premium Storage-lemezekkel. Ez az a módszer, amellyel a magasabb átviteli sebesség vagy IOPS alacsonyabb áron érhető el az Azure Premium Storage használatával. A két P15 Premium Storage-lemez közötti csíkozás például a következő átviteli sebességhez jut: 

- 250 MiB/mp. Egy ilyen kötet 512 GiB kapacitással fog rendelkezni. Ha egyetlen lemezre van szüksége, amely a 250 MiB-átviteli sebességet adja meg, egy 2 TiB kapacitású P40-lemezt kell kiválasztania. 
- 400 MiB/MP: négy P10 prémium szintű Storage-lemez, amely az 512 GiB teljes kapacitásával rendelkezik csíkozással. Ha a másodpercenként legalább 500 MiB-átviteli sebességű lemezre van szüksége, akkor egy P60 Premium Storage-lemezt kell választania 8 TiB-val. Mivel a Premium Storage díja közel van a kapacitáshoz, a költségmegtakarítást a csíkozás használatával lehet megtakarítani.

Néhány szabályt követni kell a csíkozáson:

- Nincs virtuális gépen konfigurált tárterület, mert az Azure Storage már megtartja az adatredundáns adatvédelmet
- Azok a lemezek, amelyeken a sáv be van állítva, azonos méretűnek kell lennie.

A több kisebb lemez közötti csíkozás a legjobb módszer egy jó ár/teljesítmény arány elérésére az Azure Premium Storage használatával. Tisztában van azzal, hogy a csíkozásnak van néhány további üzembe helyezési és felügyeleti terhelése.

A sávok méretére vonatkozó javaslatokért olvassa el a különböző adatbázis-kezelők dokumentációját, például [SAP HANA Azure-beli virtuális gépek tárolási konfigurációit](./hana-vm-operations-storage.md).




## <a name="next-steps"></a>Következő lépések
Olvassa el a cikkeket:

- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](./dbms_guide_general.md)
- [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](./hana-vm-operations-storage.md)
