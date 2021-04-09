---
title: SAP bevezetési Azure Virtual Machines adatbázis-kezelő üzembe helyezése SAP-munkaterheléshez | Microsoft Docs
description: SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 430af1dfcbd9c7faabfca2f3f5b80b3b3106a889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675801"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz

Ebben a dokumentumban több különböző területre is kiterjed, amelyeket figyelembe kell venni az SAP bevezetésének az Azure IaaS történő telepítésekor. Ennek a dokumentumnak az előfeltétele, hogy olvassa el az [azure Virtual Machines adatbázis-kezelő üzembe helyezése az SAP-munkaterheléshez](dbms_guide_general.md) és egyéb útmutatók az Azure-beli [SAP-munkaterheléshez](./get-started.md)című dokumentumban ismertetett szempontokat. Ez a dokumentum a Linux rendszeren és a Windows operációs rendszereken futó SAP-vel foglalkozik. Az Azure-ban a minimálisan támogatott kiadás az SAP-es 16.0.02 (2. kiadású támogatási csomag). Javasoljuk, hogy telepítse az SAP legújabb verzióját és a legújabb javítási szintet.  A minimálisan szükséges SAP-alapú 16.0.03.07 (3. kiadás, 3-as szintű támogatási csomag) használata ajánlott.  Az SAP legújabb verziója a [Megcélozott 16,0-es kiadási ütemtervben és a CR-lista adataiban](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)található.

Az SAP-alkalmazások és a telepítési adathordozók helyének kiadási támogatásával kapcsolatos további információkért tekintse meg az alábbi helyek SAP-termékek rendelkezésre állási mátrixán kívül:

- [SAP-támogatási Megjegyzés #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP-támogatási Megjegyzés #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP-támogatási Megjegyzés #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP-támogatási Megjegyzés #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Megjegyzés: az SAP-világon belüli és kívüli összes dokumentációban a termék nevét Sybase-vagy SAP-betanítási, vagy más esetekben a rendszer hivatkozik rá. Ahhoz, hogy konzisztens maradjon, az **SAP** betekintő nevet használjuk ebben a dokumentációban.

## <a name="operating-system-support"></a>Operációs rendszer támogatása
Az SAP-termékek rendelkezésre állási mátrixa tartalmazza az egyes SAP-alkalmazások támogatott operációs rendszereit és az SAP kernel-kombinációit.  A Linux-disztribúciók SUSE 12. x, SUSE 15. x, Red Hat 7. x teljes mértékben támogatottak.  Az SAP-t támogató operációs rendszerként való Oracle Linux nem támogatott.  Javasoljuk, hogy használja az elérhető legújabb Linux-kiadásokat. A Windows-ügyfeleknek a Windows Server 2016 vagy a Windows Server 2019 kiadásait kell használniuk.  A Windows korábbi kiadásai, például a Windows 2012, technikailag támogatottak, de a legújabb Windows-verzió mindig ajánlott.


## <a name="specifics-to-sap-ase-on-windows"></a>A Windowson futó SAP-központhoz kapcsolódó részletek
A Microsoft Azure-től kezdve a meglévő SAP-elősegítő alkalmazásai áttelepíthetők az Azure-Virtual Machinesba. Az Azure-beli virtuális gépeken futó SAP-bevezetéssel csökkentheti a vállalati szélességű alkalmazások üzembe helyezésének, kezelésének és karbantartásának teljes tulajdonlási költségeit azáltal, hogy egyszerűen áttelepíti ezeket az alkalmazásokat a Microsoft Azure. Az Azure-beli virtuális gépeken futó SAP bevezetéssel a rendszergazdák és a fejlesztők továbbra is használhatják a helyszínen elérhető fejlesztési és felügyeleti eszközöket.

A Microsoft Azure számos különböző virtuálisgép-típust kínál, amelyek lehetővé teszik a legkisebb SAP-rendszerek és-tájak használatát a nagy SAP-rendszerek és tájak több ezer felhasználóval való futtatásával. A különböző SAP-tanúsítvánnyal rendelkező virtuális gépek SAP-méretezési SAP-száma az [SAP-támogatási megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Az SAP-előállítók Windows rendszeren való telepítéséhez szükséges dokumentáció a [Windows rendszerhez készült SAP-telepítési útmutatóban](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html) található.

A memóriában lévő lapok zárolásával megakadályozható, hogy az SAP betekinthető adatbázis-puffere kilapozható legyen.  Ez a beállítás nagy mennyiségű memóriával rendelkező nagyméretű forgalmas rendszerekhez hasznos. További információért forduljon a BC-DB-SYB. 


## <a name="linux-operating-system-specific-settings"></a>A Linux operációs rendszerre vonatkozó beállítások
Linux rendszerű virtuális gépeken az SAP-bevezetési `saptune` linuxos Linux-lapokat alapértelmezés szerint engedélyezni kell, és a parancs használatával ellenőrizhető  

`cat /proc/meminfo` 

Az oldalméret általában 2048 KB. Részletekért tekintse [meg a Linuxon futó hatalmas lapokat](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) ismertető cikket. 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Javaslatok a virtuális gép és a lemez struktúrájához az SAP-alapú központi telepítések esetében

Az SAP NetWeaver-alkalmazások SAP-alapú bejelentési szolgáltatásait az [SAP-támogatási megjegyzésekben](https://launchpad.support.sap.com/#/notes/1928533) felsorolt virtuálisgép-típusok támogatják, #1928533 a közepes méretű SAP betekintő adatbázis-kiszolgálóinak tipikus virtuálisgép-típusai is Esv3.  A nagyméretű, több terabájtos adatbázisok képesek az M sorozatú virtuális gépek típusának kihasználására. Az M-sorozat írásgyorsítóának engedélyezésével javítható az SAP-alapú adatátviteli napló lemezének írási teljesítménye. A írásgyorsító az SAP-bevezetőknek körültekintően kell megvizsgálnia, mivel az SAP-beolvasások végzik a naplók írását.  Tekintse át az [SAP támogatási megjegyzéseit #2816580](../../how-to-enable-write-accelerator.md) és vegye fontolóra a teljesítményteszt futtatását.  
Írásgyorsító csak tranzakciónapló-lemezre lett tervezve. A lemez szintű gyorsítótárat a NONE értékre kell beállítani. Ne lepődj meg, ha az Azure írásgyorsító nem mutat hasonló fejlesztési funkciókat más adatbázis-kezelők esetében. Az SAP-nal a tranzakciónaplóba való beírása alapján előfordulhat, hogy az Azure írásgyorsító nem tud felgyorsulni.
Az adateszközökhöz és a naplózási eszközökhöz külön lemezek használata ajánlott.  A rendszeradatbázisok sybsecurity, és `saptools` nem igényelnek dedikált lemezeket, és az SAP-adatbázis adat-és naplózási eszközeit tartalmazó lemezekre helyezhetők. 

![Az SAP-hez készült tárolási konfiguráció](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Fájlrendszerek, csíkozott méret & IO-egyensúly 
Az SAP-előállítók egymás után, a lemezes tárolóeszközökbe írnak, kivéve, ha másként vannak konfigurálva. Ez azt jelenti, hogy egy üres, négy eszközzel rendelkező SAP-adatbázis csak az első eszközön fog írni.  A többi lemezes eszköz csak akkor lesz beírva, amikor az első eszköz megtelt.  Az egyes SAP-eszközök OLVASÁSI és írási IO-mennyisége valószínűleg eltérő lesz. Ha a lemezes i/o-t az összes rendelkezésre álló Azure-lemezre kiegyenlíti, a Windows-tárolóhelyek vagy a Linux-LVM2 használata szükséges. Linux rendszeren a lemezek formázásához ajánlott a XFS fájlrendszer használata. Az LVM-sáv méretét teljesítményteszttel kell tesztelni. 128 KB-os sáv mérete jó kiindulási pont. Windows rendszeren az NTFS-foglalási egység méretét (AUS) tesztelni kell. 64 KB használható kezdő értékként. 

Azt javasoljuk, hogy konfigurálja az automatikus adatbázis-kiterjesztést az [SAP adaptív kiszolgáló nagyvállalati](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)  és [SAP-támogatási megjegyzésének](https://launchpad.support.sap.com/#/notes/1815695)konfigurálásával foglalkozó cikkben leírtak szerint, #1815695. 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Az Azure-beli virtuális gépeken, a lemez-és fájlrendszer-konfigurációkon használható SAP-beszállítói minta 
Az alábbi sablonok a Linux és a Windows rendszerhez készült minta konfigurációkat mutatják be. A virtuális gép és a lemez konfigurációjának megerősítése előtt gondoskodjon arról, hogy az egyes virtuális gépek hálózati és tárolási sávszélesség-kvótái elegendőek legyenek az üzleti igények kielégítéséhez. Azt is vegye figyelembe, hogy a különböző Azure-beli virtuálisgép-típusok különböző számú lemezzel rendelkezhetnek, amelyek a virtuális géphez csatlakoztathatók. Egy E4s_v3 virtuális gép például 48 MB/s-os korláttal rendelkezik, az IO átviteli sebessége. Ha az adatbázis biztonsági mentési tevékenysége által igényelt tárolási sebesség meghaladja a 48 MB/s-ot, a nagyobb méretű virtuálisgép-típus nem elkerülhető. Az Azure Storage konfigurálásakor azt is figyelembe kell vennie, hogy különösen az [Azure Premium Storage](../../premium-storage-performance.md) esetében a kapacitás és a IOPS GB-onként változik. További információ ebben a témakörben: [milyen típusú lemezek érhetők el az Azure-ban?](../../disks-types.md). Az adott Azure-beli virtuálisgép-típusok kvótái a cikkhez kapcsolódó [memória-optimalizált virtuálisgép-méretek](../../sizes-memory.md) és cikkek című cikkben vannak dokumentálva. 

> [!NOTE]
>  Ha egy adatbázis-kezelő rendszert helyez át a helyszínről az Azure-ba, javasoljuk, hogy végezzen figyelést a virtuális gépen, és mérje fel a processzor, a memória, a IOPS és a tárterület átviteli sebességét. Hasonlítsa össze a fent említett cikkekben dokumentált virtuálisgép-kvóta korlátaival megfigyelt csúcsérték-értékeket

Az alább megadott példák szemléltető célokat szolgálnak, és az egyéni igények alapján módosíthatók. Az SAP bevezetésének kialakítása miatt az adategységek száma nem annyira kritikus, mint más adatbázisokhoz. A dokumentumban részletezett adategységek száma csak útmutató. 

Példa arra, hogy egy kis SAP-alapú, 50 GB – 250 GB közötti adatbázis-mérettel rendelkező kisméretű SAP bevezető DB-kiszolgáló konfigurációja hasonlítson a következőre:

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Gyorsított hálózatkezelés | Engedélyezés | Engedélyezés | ---|
| SAP-bemutató verziója | 16.0.03.07 vagy újabb | 16.0.03.07 vagy újabb | --- |
| adategységek száma | 4 | 4 | ---|
| naplózási eszközök száma | 1 | 1 | --- |
| ideiglenes eszközök száma | 1 | 1 | További SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/15 SP1 vagy RHEL 7,6 | --- |
| Lemez összesítése | Tárolóhelyek | LVM2 | --- |
| Fájlrendszer | NTFS | XFS |
| A blokk méretének formázása | a számítási feladatok tesztelését igényli | a számítási feladatok tesztelését igényli | --- |
| adatlemezek száma és típusa | Premium Storage: 2 x P10 (RAID0) | Premium Storage: 2 x P10 (RAID0)| Cache = csak olvasható |
| a naplózási lemezek száma és típusa | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = nincs |
| MaxMemory-paraméter | a fizikai RAM 90%-a | a fizikai RAM 90%-a | egyetlen példány feltételezve |
| biztonsági mentési eszközök száma | 4 | 4| --- |
| a biztonsági mentési lemezek száma és típusa | 1 | 1 | --- |


Példa egy közepes szintű SAP-alapú, 250 GB – 750 GB közötti adatbázis-méretű, közepes teljesítményű SAP betekintő DB-kiszolgáló konfigurálására, például egy kisebb SAP Business Suite-rendszerre

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Gyorsított hálózatkezelés | Engedélyezés | Engedélyezés | ---|
| SAP-bemutató verziója | 16.0.03.07 vagy újabb | 16.0.03.07 vagy újabb | --- |
| adategységek száma | 8 | 8 | ---|
| naplózási eszközök száma | 1 | 1 | --- |
| ideiglenes eszközök száma | 1 | 1 | További SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/15 SP1 vagy RHEL 7,6 | --- |
| Lemez összesítése | Tárolóhelyek | LVM2 | --- |
| Fájlrendszer | NTFS | XFS |
| A blokk méretének formázása | a számítási feladatok tesztelését igényli | a számítási feladatok tesztelését igényli | --- |
| adatlemezek száma és típusa | Premium Storage: 4 x P20 (RAID0) | Premium Storage: 4 x P20 (RAID0)| Cache = csak olvasható |
| a naplózási lemezek száma és típusa | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = nincs |
| MaxMemory-paraméter | a fizikai RAM 90%-a | a fizikai RAM 90%-a | egyetlen példány feltételezve |
| biztonsági mentési eszközök száma | 4 | 4| --- |
| a biztonsági mentési lemezek száma és típusa | 1 | 1 | --- |

Példa arra, hogy egy kis SAP-beli, 750 GB – 2000 GB közötti adatbázis-mérettel rendelkező kisméretű SAP-alapú adatbázis-kiszolgáló konfigurációját, például egy nagyobb SAP Business Suite-rendszerre hasonlít

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Gyorsított hálózatkezelés | Engedélyezés | Engedélyezés | ---|
| SAP-bemutató verziója | 16.0.03.07 vagy újabb | 16.0.03.07 vagy újabb | --- |
| adategységek száma | 16 | 16 | ---|
| naplózási eszközök száma | 1 | 1 | --- |
| ideiglenes eszközök száma | 1 | 1 | További SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/15 SP1 vagy RHEL 7,6 | --- |
| Lemez összesítése | Tárolóhelyek | LVM2 | --- |
| Fájlrendszer | NTFS | XFS |
| A blokk méretének formázása | a számítási feladatok tesztelését igényli | a számítási feladatok tesztelését igényli | --- |
| adatlemezek száma és típusa | Premium Storage: 4 x P30 (RAID0) | Premium Storage: 4 x P30 (RAID0)| Cache = csak olvasható |
| a naplózási lemezek száma és típusa | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = nincs |
| MaxMemory-paraméter | a fizikai RAM 90%-a | a fizikai RAM 90%-a | egyetlen példány feltételezve |
| biztonsági mentési eszközök száma | 4 | 4| --- |
| a biztonsági mentési lemezek száma és típusa | 1 | 1 | --- |


Példa arra, hogy egy kisebb SAP-t tartalmazó adatbázis-kiszolgáló konfigurációját 2 TB +, például egy nagyobb globálisan használt SAP Business Suite-rendszer, a következőhöz hasonlóan kell kinéznie:

| Konfiguráció | Windows | Linux | Megjegyzések |
| --- | --- | --- | --- |
| Virtuális gép típusa | M sorozat (1,0 – 4,0 TB RAM)  | M sorozat (1,0 – 4,0 TB RAM) | --- |
| Gyorsított hálózatkezelés | Engedélyezés | Engedélyezés | ---|
| SAP-bemutató verziója | 16.0.03.07 vagy újabb | 16.0.03.07 vagy újabb | --- |
| adategységek száma | 32 | 32 | ---|
| naplózási eszközök száma | 1 | 1 | --- |
| ideiglenes eszközök száma | 1 | 1 | További SAP BW munkaterhelés |
| Operációs rendszer | Windows Server 2019 | SUSE 12 SP4/15 SP1 vagy RHEL 7,6 | --- |
| Lemez összesítése | Tárolóhelyek | LVM2 | --- |
| Fájlrendszer | NTFS | XFS |
| A blokk méretének formázása | a számítási feladatok tesztelését igényli | a számítási feladatok tesztelését igényli | --- |
| adatlemezek száma és típusa | Premium Storage: 4 + x P30 (RAID0) | Premium Storage: 4 + x P30 (RAID0)| Cache = csak olvasható, megfontolandó az Azure Ultra Disk |
| a naplózási lemezek száma és típusa | Premium Storage: 1 x P20  | Premium Storage: 1 x P20 | Cache = nincs, tekintse meg az Azure Ultra diskt |
| MaxMemory-paraméter | a fizikai RAM 90%-a | a fizikai RAM 90%-a | egyetlen példány feltételezve |
| biztonsági mentési eszközök száma | 16 | 16 | --- |
| a biztonsági mentési lemezek száma és típusa | 4 | 4 | LVM2/tárolóhelyek használata |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>A biztonsági mentés & az Azure-beli SAP-előkészítő szempontjai
Az adat-és biztonságimásolat-eszközök számának növelése növeli a biztonsági mentési és a visszaállítási teljesítményt. Azt javasoljuk, hogy az SAP-alapú biztonsági mentési eszközt futtató Azure-lemezeket a korábban bemutatott táblázatokban ábrázolva jelenítse meg. Ügyelni kell a biztonsági mentési eszközök és lemezek számának kiegyensúlyozására, és biztosítania kell, hogy a biztonsági mentési teljesítmény ne haladja meg a virtuális gép átviteli kvótájának 40%-50%-át. Az SAP biztonsági mentési tömörítést ajánlott alapértelmezettként használni. További részleteket a cikkekben találhat:

- [SAP-támogatási Megjegyzés #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP-támogatási Megjegyzés #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP-támogatási Megjegyzés #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Ne használja a meghajtó D:\ vagy/Temp helyet az adatbázis vagy a napló memóriaképének célhelye.

### <a name="impact-of-database-compression"></a>Az adatbázis-tömörítés hatása
Azokon a konfigurációkban, amelyekben az I/O-sávszélesség korlátozási tényezővé válhat, a mértékek, amelyek csökkentik a IOPS, segíthetnek a munkaterhelések kiosztásában egy olyan IaaS-forgatókönyvben, mint amilyen Ezért azt javasoljuk, hogy a meglévő SAP-adatbázisok Azure-ba való feltöltése előtt győződjön meg arról, hogy az SAP-vel történő adattömörítés használatban van.

Az Azure-ba való feltöltés előtt a tömörítés alkalmazására vonatkozó javaslat több okból is fennáll:

* Az Azure-ba feltölteni kívánt adatmennyiség alacsonyabb
* A tömörítés végrehajtásának időtartama rövidebb, feltételezve, hogy az egyik nagyobb CPU-igényű vagy magasabb I/O-sávszélességgel vagy kisebb I/O-késéssel rendelkező erősebb hardvert használhat.
* A kisebb adatbázis-méretek kevesebb költséget okozhatnak a lemez kiosztása során

Az adatkezelési és a LOB-Compression az Virtual Machines Azure-ban üzemeltetett virtuális gépeken működnek, mint a helyszínen. Ha további információt szeretne arról, hogy miként ellenőrizhető, hogy a tömörítés már használatban van-e egy meglévő SAP-adatbázison, tekintse meg az [SAP-támogatási megjegyzés 1750510](https://launchpad.support.sap.com/#/notes/1750510). Az SAP betekintő adatbázis-tömörítési szolgáltatásával kapcsolatos további információkért lásd [: SAP-támogatás megjegyzés #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Az Azure-beli SAP-bekapcsolás magas rendelkezésre állása 
A HADR felhasználói útmutatója a 2 csomópontos SAP-alapú "mindig-on" megoldás beállítását és konfigurációját ismerteti.  Emellett a harmadik vész-helyreállítási csomópont is támogatott. Az SAP-benyújtó számos magas rendelkezésre állású konfigurációt támogat, többek között a megosztott lemezeket és a natív operációsrendszer-fürtözést. Az Azure-ban az egyetlen támogatott konfiguráció, ha a fault Managert nem a lebegőpontos IP-cím nélkül használja.  A lebegőpontos IP-cím metódus nem fog működni az Azure-ban.  Az SAP-kernel egy "HA aware" alkalmazás, és ismeri az elsődleges és a másodlagos SAP-benyújtó kiszolgálókat. Az SAP bevezetési és az Azure közötti integráció nem áll fenn, az Azure belső terheléselosztó nincs használatban. Ezért a szabványos SAP-bevezető dokumentációt kell követnie az [SAP-HADR felhasználói útmutatójának](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) kiindulásával 

> [!NOTE]
> Az Azure-ban az egyetlen támogatott konfiguráció, ha a fault Managert nem a lebegőpontos IP-cím nélkül használja.  A lebegőpontos IP-cím metódus nem fog működni az Azure-ban. 

### <a name="third-node-for-disaster-recovery"></a>Harmadik csomópont a vész-helyreállításhoz
A magas rendelkezésre állású SAP-Always-On használata esetén előfordulhat, hogy egy másik Azure-régióban lévő aszinkron módon replikált csomópontra szeretné kiterjeszteni a konfigurációt. Az ilyen forgatókönyvek dokumentációja [itt](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)található.

## <a name="sap-ase-database-encryption--ssl"></a>SAP-alapú adatbázis-titkosítási & SSL 
Az SAP Software kiépítési kezelője (SWPM) lehetőséget ad az adatbázis titkosítására a telepítés során.  Ha titkosítást szeretne használni, javasoljuk, hogy az SAP teljes adatbázis-titkosítást használja.  Tekintse meg a dokumentációban ismertetett részleteket:

- [SAP-támogatási Megjegyzés #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP-támogatási Megjegyzés #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP-támogatási Megjegyzés #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP-támogatási Megjegyzés #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Ha egy SAP-alapú előkészítő adatbázis titkosítva van, akkor a biztonsági mentési memóriakép tömörítése nem fog működni. Lásd még: [SAP-támogatás megjegyzés #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>SAP-bevezetési ellenőrzőlista az Azure üzembe helyezéséhez
 
- SAP-beli vagy magasabb szintű 16.0.03.07 üzembe helyezése
- Frissítés a legújabb verzióra, valamint a FaultManager és a SAPHostAgent javításai
- Üzembe helyezés a legújabb tanúsítvánnyal rendelkező operációs rendszeren, például Windows 2019, SUSE 15,1 vagy RedHat 7,6 vagy újabb verzió
- SAP-tanúsítvánnyal rendelkező virtuális gépek használata – a nagy memóriaú Azure-beli virtuálisgép-SKU-ket, például a Es_v3 vagy az M-sorozatú, x-sorozatú VM-ket
- Egyezzen meg a lemez IOPS és a virtuális gép összesített átviteli sebességével a lemez kialakításával.  Elegendő számú lemez üzembe helyezése
- Windows Storage Spaces vagy Linux LVM2 használatával összesített lemezek a megfelelő szalagos mérettel és fájlrendszerrel
- Elegendő számú eszközt hozhat létre az adatkezeléshez, a naplóhoz, a temphoz és a biztonsági mentéshez.
- Használjon UltraDisk-t az x-Large rendszerekhez 
- SAP-bevezetési `saptune` Linux operációs rendszer futtatása 
- Az adatbázis védelme DB titkosítással – a kulcsok kézi tárolása Azure Key Vault 
- Az [SAP on Azure ellenőrzőlista](./sap-deployment-checklist.md) befejezése 
- Napló biztonsági mentésének és teljes biztonsági mentésének konfigurálása 
- Tesztelés a HA/DR, biztonsági mentés és visszaállítás és a stressz & a mennyiségi teszt végrehajtása 
- Az automatikus adatbázis-bővítmény működésének megerősítése 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Az DBACockpit használata az adatbázis-példányok figyelésére
Az SAP-t adatbázis-platformként használó SAP-rendszerek esetén a DBACockpit a tranzakciós DBACockpit vagy WebDynpro a beágyazott böngészőablakként érhető el. Az adatbázis figyelésének és felügyeletének teljes funkcionalitása azonban csak a DBACockpit WebDynpro implementációjában érhető el.

Csakúgy, mint a helyszíni rendszerek esetében, több lépésre van szükség ahhoz, hogy a DBACockpit WebDynpro-implementációja által használt összes SAP NetWeaver funkció engedélyezve legyen. A webdynpros használatának engedélyezéséhez és a szükségesek létrehozásához kövesse az [SAP támogatási megjegyzéseit #1245200](https://launchpad.support.sap.com/#/notes/1245200) . Ha követi a fenti megjegyzések utasításait, az Internet Communication Manager ( `ICM` ) és a http-és HTTPS-kapcsolatokhoz használt portok mellett is konfigurálhatja. A http alapértelmezett beállítása a következőképpen néz ki:

> ICM/server_port_0 = PROT = HTTP, PORT = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = PROT = HTTPS, PORT = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

a tranzakciós DBACockpit létrehozott hivatkozások a következőhöz hasonlóan néz ki:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> 

Attól függően, hogy az SAP-szolgáltatást üzemeltető Azure-beli virtuális gép hogyan kapcsolódik az AD-hez és a DNS-hez, meg kell győződnie arról, hogy az ICM teljes állomásnevet használ, amely feloldható azon a gépen, amelyen a DBACockpit megnyitja. Lásd: [SAP-támogatás megjegyzés #773830](https://launchpad.support.sap.com/#/notes/773830) annak megismeréséhez, hogy az ICM hogyan határozza meg a teljes állomásnevet a profil paraméterei alapján, és ha szükséges, állítsa be explicit módon az icm/host_name_full paramétert.

Ha a virtuális gépet olyan Cloud-Only környezetben telepítette, amely a helyszíni és az Azure közötti kapcsolat nélkül üzemel, meg kell határoznia egy nyilvános IP-címet és a-t `domainlabel` . A virtuális gép nyilvános DNS-nevének formátuma így néz ki:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

A DNS-névvel kapcsolatos további részleteket [itt] [Virtual-Machines-azurerm-versus-azuresm] találhat.

Ha az ICM/host_name_full SAP-profil paramétert az Azure-beli virtuális gép DNS-nevére állítja be, a hivatkozás a következőhöz hasonló lehet:

> https: \/ /mydomainlabel.westeurope.cloudapp.net:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.net:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

Ebben az esetben a következőket kell tennie:

* Bejövő szabályok hozzáadása a hálózati biztonsági csoporthoz az ICM-sel való kommunikációhoz használt TCP/IP-portok Azure Portaljában
* Bejövő szabályok hozzáadása az ICM-sel való kommunikációhoz használt TCP/IP-portok Windows tűzfal konfigurációjához

Az összes rendelkezésre álló javítás automatikus importálása esetén ajánlott rendszeresen alkalmazni az SAP-verzióra vonatkozó korrekciós gyűjtemény SAP-megjegyzését:

* [SAP-támogatási Megjegyzés #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP-támogatási Megjegyzés #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP-támogatási Megjegyzés #1882376](https://launchpad.support.sap.com/#/notes/1882376)

A következő SAP-megjegyzésekben talál további információt az SAP-vel kapcsolatos DBA Pilótafülkéről:

* [SAP-támogatási Megjegyzés #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP-támogatási Megjegyzés #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP-támogatási Megjegyzés #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP-támogatási Megjegyzés #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP-támogatási Megjegyzés #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP-támogatási Megjegyzés #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP-támogatási Megjegyzés #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP-támogatási Megjegyzés #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Hasznos hivatkozások, megjegyzések & az SAP bevezetői tanulmányai
Az SAP bevezető [16.0.03.07 dokumentációjának](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) kezdőlapja különböző dokumentumokra mutató hivatkozásokat tartalmaz, amelyek a következő dokumentumokat ismertetik:

- SAP-vel kapcsolatos tanulási folyamat – felügyelet & monitorozás
- SAP-alapú tanulás – a telepítési & frissítése

hasznosak. Egy másik hasznos dokumentum [: SAP-alkalmazások az SAP adaptív Server Enterprise ajánlott eljárások az áttelepítéshez és a futtatókörnyezethez](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

További hasznos SAP-támogatási megjegyzések:

- [SAP-támogatási Megjegyzés #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP-támogatási Megjegyzés #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP-támogatási Megjegyzés #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP-támogatási Megjegyzés #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP-támogatási Megjegyzés #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP-támogatási Megjegyzés #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP-támogatási Megjegyzés #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-támogatási Megjegyzés #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP-támogatási Megjegyzés #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP-támogatási Megjegyzés #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP-támogatási Megjegyzés #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP-támogatási Megjegyzés #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-támogatási Megjegyzés #1588316](https://launchpad.support.sap.com/#/notes/158831) 

További információ a következő címen érhető el 

- [SAP-alkalmazások az SAP adaptív kiszolgáló vállalatán](https://community.sap.com/topics/applications-on-ase)
- [SAP-beli Infoközpont](http://infocenter.sybase.com/help/index.jsp) 
- [SAP-beüzemelési always-on 3rd DR Node telepítő](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

A havi hírlevél [SAP-támogatási megjegyzéssel](https://launchpad.support.sap.com/#/notes/2381575) jelenik meg #2381575 


## <a name="next-steps"></a>Következő lépések
Az Azure-beli SAP-munkaterhelések című cikkben tájékozódhat [: tervezési és üzembe helyezési ellenőrzőlista](./sap-deployment-checklist.md)