---
title: Az Azure-beli SAP HANA támogatott forgatókönyvei (nagyméretű példányok) | Microsoft Docs
description: Támogatott forgatókönyvek és az Azure-beli SAP HANA architektúrájának részletei (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a49c55da0bb8018b3faa7c6f70b02e79d9478603
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666677"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>A HANA nagyméretű példányainak támogatott forgatókönyvei
Ez a cikk a HANA Large instances (HLI) támogatott forgatókönyveit és architektúrájának részleteit ismerteti.

>[!NOTE]
>Ha a szükséges forgatókönyv nem szerepel ebben a cikkben, vegye fel a kapcsolatot a Microsoft Service Management csapatával a követelmények felmérése érdekében.
A HLI egység beállítása előtt ellenőrizze a kialakítást az SAP vagy a szolgáltatás implementációs partnere között.

## <a name="terms-and-definitions"></a>Feltételek és definíciók
Ismerkedjen meg a cikkben használt feltételekkel és definíciókkal:

- **SID**: a HANA rendszer rendszerazonosítója
- **HLI**: Hana nagyméretű példányok
- **Dr**: vész-helyreállítás
- **Normál Dr**: a rendszer telepítése dedikált erőforrással kizárólag Dr célra
- **Többcélú Dr**: egy Dr-helyrendszer, amely egy Dr eseményhez konfigurált éles környezetben nem éles környezet használatára van konfigurálva. 
- **Egyszeri SID**: egy telepített rendszer egy példányával
- **Többszörös SID**: több példányban konfigurált rendszer; más néven MCOS-környezet
- **HSR**: SAP HANA rendszerreplikáció

## <a name="overview"></a>Áttekintés
A HANA Large instances számos architektúrát támogat az üzleti követelmények teljesítéséhez. Az alábbi fejezetek az építészeti forgatókönyveket és azok konfigurációs részleteit ismertetik. 

A származtatott architektúra kialakítása kizárólag infrastruktúra szempontjából hasznos, és az SAP-t vagy a saját implementációs partnereit kell megadnia a HANA üzembe helyezéséhez. Ha a forgatókönyvek nem szerepelnek a jelen cikkben, lépjen kapcsolatba a Microsoft-fiók csapatával, és tekintse át az architektúrát, és származtatja a megoldást.

> [!NOTE]
> Ezek az architektúrák teljes mértékben megfelelnek a testreszabott adatintegrációs (TDI) kialakításnak és az SAP által támogatottnak.

Ez a cikk az egyes Támogatott architektúrák két összetevőjének részleteit ismerteti:

- Ethernet
- Tárolás

### <a name="ethernet"></a>Ethernet

Minden egyes kiépített kiszolgáló az előre konfigurált Ethernet-adapterek készletével van konfigurálva. Az egyes HLI konfigurált Ethernet-adapterek négy típusra vannak kategorizálva:

- **A**: a vagy az ügyfél-hozzáféréshez használatos.
- **B**: csomópont-csomópont típusú kommunikációhoz használatos. Ez az interfész minden kiszolgálón konfigurálva van (függetlenül a kért topológiától), de csak a kibővített forgatókönyvek esetében használatos.
- **C**: csomópontok közötti kapcsolathoz használatos.
- **D**: csomópontok közötti STONITH-telepítéshez használatos. Ez az illesztőfelület csak akkor van konfigurálva, ha HSR-telepítést kér.  

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópont – csomópont|
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | STONITH |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Csomópont – csomópont|
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | STONITH |

A felületet a HLI egységen konfigurált topológia alapján választhatja ki. A "B" illesztőfelület például a csomópontok közötti kommunikációhoz van beállítva, ami akkor lehet hasznos, ha kibővíthető topológia van konfigurálva. Ez az illesztőfelület nem használatos egyetlen csomóponthoz, Felskálázási konfigurációhoz. Az illesztőfelület-használattal kapcsolatos további információkért tekintse át a szükséges forgatókönyveket (a cikk későbbi részében). 

Szükség esetén további hálózati adapter-kártyákat is meghatározhat saját maga. A meglévő hálózati adapterek konfigurációi azonban *nem* módosíthatók.

>[!NOTE]
>Előfordulhat, hogy olyan további csatolókat talál, amelyek fizikai felületek vagy kötések. A használati esethez csak a korábban említett illesztőfelületeket érdemes figyelembe venni. Minden más figyelmen kívül hagyható.

A két hozzárendelt IP-címmel rendelkező egységek eloszlásának a következőhöz hasonlóan kell kinéznie:

- Az "A" Ethernethez hozzárendelt IP-címmel kell rendelkeznie, amely a Microsoft számára elküldött kiszolgálói IP-címkészlet címén belül van. Ezt az IP-címet az operációs rendszer */etc/hosts* könyvtárában kell megőrizni.

- A "C" Ethernethez hozzá kell rendelni egy hozzárendelt IP-címet, amelyet a rendszer az NFS-kommunikációhoz használ. Ezt a címeket *nem* kell fenntartani a *etc/hosts* könyvtárban, hogy lehetővé váljon a példányok közötti forgalom a bérlőn belül.

A HANA rendszer replikálása vagy a HANA kibővítő üzembe helyezése esetén a két hozzárendelt IP-címmel rendelkező panel-konfiguráció nem megfelelő. Ha csak két hozzárendelt IP-címmel rendelkezik, és szeretné központilag telepíteni ezt a konfigurációt, lépjen kapcsolatba SAP HANA az Azure Service Management szolgáltatásban. Harmadik IP-címet a harmadik VLAN-ban is hozzárendelhet. Három hálózati adapter-porton három hozzárendelt IP-címmel rendelkező HANA Large instances-egység esetén a következő használati szabályok érvényesek:

- Az "A" Ethernetnek rendelkeznie kell egy hozzárendelt IP-címmel, amely a Microsoft számára elküldött kiszolgálói IP-címkészlet tartományán kívül esik. Ezt az IP-címet nem szabad karbantartani az operációs rendszer *etc/hosts* könyvtárában.

- A "B" Ethernetet kizárólag az *etc/hosts* könyvtárban kell fenntartani a különböző példányok közötti kommunikációhoz. Ezek a kibővíthető HANA-konfigurációkban fenntartott IP-címek, amelyek a HANA által a csomópontok közötti konfigurációhoz használt IP-címek.

- A "C" Ethernetnek rendelkeznie kell egy, az NFS-tárolóval való kommunikációhoz használt hozzárendelt IP-címmel. Az ilyen típusú címeket nem szabad karbantartani az *etc/hosts* könyvtárban.

- A "D" Ethernetet kizárólag a szívritmus-STONITH eszközeinek eléréséhez szabad használni. Ez az interfész akkor szükséges, ha a HANA rendszerreplikációt konfigurálja, és az operációs rendszer automatikus feladatátvételét szeretné elérni egy SBD-alapú eszköz használatával.


### <a name="storage"></a>Tárolás
A tárterület előre konfigurálva van a kért topológia alapján. A kötetek mérete és a csatlakoztatási pontok a kiszolgálók számától, az SKU-számtól és a konfigurált topológiától függően változnak. További információkért tekintse át a szükséges forgatókönyveket (a cikk későbbi részében). Ha több tárterületre van szüksége, akkor 1 TB-os növekményekben vásárolhatja meg.

>[!NOTE]
>A csatlakoztatási pont/usr/SAP/az \<SID> /Hana/Shared csatlakoztatási pontjának szimbolikus hivatkozása.


## <a name="supported-scenarios"></a>Támogatott esetek

A következő szakaszban található architektúra-diagramok a következő jelöléseket használják:

[![Architektúra-diagramok táblázata](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

A támogatott forgatókönyvek a következők:

* Egyetlen csomópont egyetlen SID-vel
* Egyetlen csomópontos MCOS
* Egyetlen csomópont DR-vel (normál)
* Egyetlen csomópont DR-vel (többcélú)
* HSR a STONITH
* HSR DR-vel (normál/többcélú) 
* Gazdagép automatikus feladatátvétele (1 + 1) 
* Kibővíthető készenléti állapottal
* Vertikális felskálázás készenlét nélkül
* Vertikális felskálázás DR-vel

## <a name="single-node-with-one-sid"></a>Egyetlen csomópont egyetlen SID-vel

Ez a topológia egyetlen SID-vel támogatja a skálázási konfiguráció egyik csomópontját.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópont egyetlen SID-vel](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA-telepítés | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése | 
|/hana/logbackups/SID | Naplók visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása

## <a name="single-node-mcos"></a>Egyetlen csomópontos MCOS

Ez a topológia egyetlen csomópontot támogat egy több SID-t tartalmazó Felskálázási konfigurációban.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópontos MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID1 | HANA-telepítés a SID1 | 
|/hana/data/SID1/mnt00001 | Az adatfájlok telepítése a SID1 | 
|/hana/log/SID1/mnt00001 | Naplófájlok telepítése a SID1 | 
|/hana/logbackups/SID1 | SID1-naplók visszaállítása |
|/hana/shared/SID2 | HANA-telepítés a SID2 | 
|/hana/data/SID2/mnt00001 | Az adatfájlok telepítése a SID2 | 
|/hana/log/SID2/mnt00001 | Naplófájlok telepítése a SID2 | 
|/hana/logbackups/SID2 | SID2-naplók visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A kötet méretének eloszlása a memória adatbázisának méretétől függ. Ha szeretné megtudni, hogy a memóriában hány adatbázis-méret támogatott a több SID-alapú környezetben, tekintse meg az [Áttekintés és az architektúra](./hana-overview-architecture.md)című témakört.

## <a name="single-node-with-dr-using-storage-replication"></a>Önálló csomópont a Storage-replikációt használó DR használatával
 
Ez a topológia egy vagy több SID-vel rendelkező méretezési konfiguráció egyik csomópontját támogatja, és az elsődleges SID DR helyének Storage-alapú replikálásával. A diagramon csak egyetlen SID rendszer látható az elsődleges helyen, de a MCOS rendszerek is támogatottak.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Önálló csomópont a Storage-replikációt használó DR használatával](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA-telepítés SID-hez | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése SID-hez | 
|/hana/logbackups/SID | SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memória adatbázisának méretétől függ. Ha szeretné megtudni, hogy a memóriában hány adatbázis-méret támogatott a több SID-alapú környezetben, tekintse meg az [Áttekintés és az architektúra](./hana-overview-architecture.md)című témakört.
- A DR helyen: a kötetek és a csatlakoztatási pontok konfigurálva vannak ("a HANA telepítéséhez szükséges" jelöléssel) a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR helyen: az adatokat, a naplók biztonsági mentését és a megosztott köteteket (a "Storage Replication" jelöléssel jelölte meg) az üzemi helyről pillanatképen keresztül replikálja a rendszer. Ezek a kötetek csak feladatátvételkor vannak csatlakoztatva. További információ: vész- [helyreállítási feladatátvételi eljárás](./hana-overview-high-availability-disaster-recovery.md).
- A rendszer az *I. típusú SKU* rendszerindító kötetét a Dr csomópontba replikálja.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Egyetlen csomópont DR (többcélú) tároló-replikáció használatával
 
Ez a topológia egy vagy több SID-vel rendelkező méretezési konfiguráció egyik csomópontját támogatja, és az elsődleges SID DR helyének Storage-alapú replikálásával. A diagramon csak egyetlen SID rendszer látható az elsődleges helyen, de a többszörös SID-(MCOS-) rendszerek is támogatottak. A DR helyen a HLI egységet használják a QA-példányhoz, miközben az elsődleges helyről futnak üzemi műveletek. A DR feladatátvétel (vagy feladatátvételi teszt) során a rendszer leállítja a DR helyen található QA-példányt.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópont DR (többcélú) tároló-replikáció használatával](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-hez | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-hez |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memória adatbázisának méretétől függ. Ha szeretné megtudni, hogy a memóriában hány adatbázis-méret támogatott a több SID-alapú környezetben, tekintse meg az [Áttekintés és az architektúra](./hana-overview-architecture.md)című témakört.
- A DR helyen: a kötetek és a csatlakoztatási pontok konfigurálva vannak ("a HANA telepítéséhez szükséges" jelöléssel) a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR helyen: az adatokat, a naplók biztonsági mentését és a megosztott köteteket (a "Storage Replication" jelöléssel jelölte meg) az üzemi helyről pillanatképen keresztül replikálja a rendszer. Ezek a kötetek csak feladatátvételkor vannak csatlakoztatva. További információ: vész- [helyreállítási feladatátvételi eljárás](./hana-overview-high-availability-disaster-recovery.md). 
- A DR helyen: a QA-példány telepítéséhez be kell állítani az adatokat, a naplók biztonsági mentését, a naplót és a közös köteteket a QA számára (a "QA példány telepítése" jelöléssel).
- A rendszer az *I. típusú SKU* rendszerindító kötetét a Dr csomópontba replikálja.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR és STONITH a magas rendelkezésre állás érdekében
 
Ez a topológia két csomópontot támogat a HANA rendszer-replikációs konfigurációhoz. Ez a konfiguráció csak egyetlen HANA-példány esetén támogatott a csomóponton. Ez azt jelenti, hogy a MCOS-forgatókönyvek *nem* támogatottak.

> [!NOTE]
> December 2019-én ez az architektúra csak a SUSE operációs rendszer esetében támogatott.


### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR és STONITH a magas rendelkezésre állás érdekében](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | A STONITH esetében használatos |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | A STONITH esetében használatos |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A másodlagos csomóponton**|
|/hana/shared/SID | HANA-telepítés másodlagos SID-hez | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése másodlagos SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése másodlagos SID-hez | 
|/hana/logbackups/SID | Másodlagos SID-naplók visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memória adatbázisának méretétől függ. Ha szeretné megtudni, hogy a memóriában hány adatbázis-méret támogatott a több SID-alapú környezetben, tekintse meg az [Áttekintés és az architektúra](./hana-overview-architecture.md)című témakört.
- STONITH: egy SBD van konfigurálva a STONITH-telepítőhöz. A STONITH használata azonban nem kötelező.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Magas rendelkezésre állás a HSR és a DR tároló-replikálással
 
Ez a topológia két csomópontot támogat a HANA rendszer-replikációs konfigurációhoz. A normál és a többcélú DRs is támogatott. Ezek a konfigurációk csak egyetlen HANA-példány esetén támogatottak egy csomóponton. Ez azt jelenti, hogy ezekhez a konfigurációkhoz a MCOS-forgatókönyvek *nem* támogatottak.

A diagramon egy többcélú forgatókönyv látható a DR helyen, ahol a HLI egységet használják a QA-példányhoz, miközben az elsődleges helyről futnak az üzemi műveletek. A DR feladatátvétel (vagy feladatátvételi teszt) során a rendszer leállítja a DR helyen található QA-példányt. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Magas rendelkezésre állás a HSR és a DR tároló-replikálással](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | A STONITH esetében használatos |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | A STONITH esetében használatos |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges hely elsődleges csomópontján**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**Az elsődleges hely másodlagos csomópontján**|
|/hana/shared/SID | HANA-telepítés másodlagos SID-hez | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése másodlagos SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése másodlagos SID-hez | 
|/hana/logbackups/SID | Másodlagos SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-hez | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-hez |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memória adatbázisának méretétől függ. Ha szeretné megtudni, hogy a memóriában hány adatbázis-méret támogatott a több SID-alapú környezetben, tekintse meg az [Áttekintés és az architektúra](./hana-overview-architecture.md)című témakört.
- STONITH: egy SBD van konfigurálva a STONITH-telepítőhöz. A STONITH használata azonban nem kötelező.
- A DR helyen: az elsődleges és a másodlagos csomópont replikálásához *két tárolási kötet szükséges* .
- A DR helyen: a kötetek és a csatlakoztatási pontok konfigurálva vannak ("a HANA telepítéséhez szükséges" jelöléssel) a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR helyen: az adatokat, a naplók biztonsági mentését és a megosztott köteteket (a "Storage Replication" jelöléssel jelölte meg) az üzemi helyről pillanatképen keresztül replikálja a rendszer. Ezek a kötetek csak feladatátvételkor vannak csatlakoztatva. További információ: vész- [helyreállítási feladatátvételi eljárás](./hana-overview-high-availability-disaster-recovery.md). 
- A DR helyen: a QA-példány telepítéséhez be kell állítani az adatokat, a naplók biztonsági mentését, a naplót és a közös köteteket a QA számára (a "QA példány telepítése" jelöléssel).
- A rendszer az *I. típusú SKU* rendszerindító kötetét a Dr csomópontba replikálja.


## <a name="host-auto-failover-11"></a>Gazdagép automatikus feladatátvétele (1 + 1)
 
Ez a topológia két csomópontot támogat a gazdagép automatikus feladatátvételi konfigurációjában. A fő-és feldolgozói szerepkörrel rendelkező csomópontok egyike, a másik pedig készenléti állapotban van. *Az SAP ezt a forgatókönyvet csak az S/4 HANA esetében támogatja.* További információ: OSS- [megjegyzés 2408419 – SAP S/4HANA – több csomópontos támogatás](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Architektúradiagram  

![Gazdagép automatikus feladatátvétele (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópont-csomópont típusú kommunikáció |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Csomópont-csomópont típusú kommunikáció |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A fő és a készenléti csomópontokon**|
|/hana/shared | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |



### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- Készenléti állapotban: a kötetek és a csatlakoztatási pontok konfigurálva vannak ("a HANA telepítéséhez szükséges" jelöléssel) a HANA-példány telepítéséhez a készenléti egységben.
 

## <a name="scale-out-with-standby"></a>Kibővíthető készenléti állapottal
 
Ez a topológia több csomópontot támogat egy kibővíthető konfigurációban. A főkiszolgálói szerepkörrel, egy vagy több, feldolgozói szerepkörrel rendelkező csomóponttal, valamint egy vagy több, készenléti állapotú csomóponttal rendelkezik. Egy adott időpontban azonban csak egyetlen fő csomópont lehet.


### <a name="architecture-diagram"></a>Architektúradiagram  

![Kibővíthető készenléti állapottal](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópont-csomópont típusú kommunikáció |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Csomópont-csomópont típusú kommunikáció |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A fő, a feldolgozói és a készenléti csomópontok**|
|/hana/shared | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


## <a name="scale-out-without-standby"></a>Vertikális felskálázás készenlét nélkül
 
Ez a topológia több csomópontot támogat egy kibővíthető konfigurációban. Létezik egy főkiszolgálói szerepkörrel rendelkező csomópont, és egy vagy több, feldolgozói szerepkörrel rendelkező csomópont. Egy adott időpontban azonban csak egyetlen fő csomópont lehet.


### <a name="architecture-diagram"></a>Architektúradiagram  

![Vertikális felskálázás készenlét nélkül](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópont-csomópont típusú kommunikáció |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Csomópont-csomópont típusú kommunikáció |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A fő-és munkavégző csomópontokon**|
|/hana/shared | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása

## <a name="scale-out-with-dr-using-storage-replication"></a>Méretezés a DR használatával a Storage replikálásával
 
Ez a topológia több csomópontot támogat a kibővíthető DR-ben. A normál és a többcélú DRs is támogatott. A diagramon csak az egyetlen cél DR látható. Ezt a topológiát készenléti csomóponttal vagy anélkül is kérheti.


### <a name="architecture-diagram"></a>Architektúradiagram  

![Méretezés a DR használatával a Storage replikálásával](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópont-csomópont típusú kommunikáció |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Csomópont-csomópont típusú kommunikáció |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/shared | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR csomóponton**|
|/hana/shared | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
-  A DR helyen: a kötetek és a csatlakoztatási pontok konfigurálva vannak ("a HANA telepítéséhez szükséges" jelöléssel) a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR helyen: az adatokat, a naplók biztonsági mentését és a megosztott köteteket (a "Storage Replication" jelöléssel jelölte meg) az üzemi helyről pillanatképen keresztül replikálja a rendszer. Ezek a kötetek csak feladatátvételkor vannak csatlakoztatva. További információ: vész- [helyreállítási feladatátvételi eljárás](./hana-overview-high-availability-disaster-recovery.md). 
- A rendszer az *I. típusú SKU* rendszerindító kötetét a Dr csomópontba replikálja.


## <a name="single-node-with-dr-using-hsr"></a>Egyetlen csomópont a DR használatával HSR
 
Ez a topológia egyetlen SID-vel támogatja a Felskálázási konfiguráció egyik csomópontját, és a HANA rendszer replikálja a DR helyet az elsődleges biztonsági azonosítóhoz. A diagramon csak egyetlen SID rendszer látható az elsődleges helyen, de a többszörös SID-(MCOS-) rendszerek is támogatottak.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópont a DR használatával HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak a HLI-egységeken (elsődleges és DR):

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA-telepítés SID-hez | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése SID-hez | 
|/hana/logbackups/SID | SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memória adatbázisának méretétől függ. Ha szeretné megtudni, hogy a memóriában hány adatbázis-méret támogatott a több SID-alapú környezetben, tekintse meg az [Áttekintés és az architektúra](./hana-overview-architecture.md)című témakört.
- Az elsődleges csomópont a DR csomóponttal szinkronizálódik a HANA rendszerreplikáció használatával. 
- A [Global REACH](../../../expressroute/expressroute-global-reach.md) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy a helyi hálózatok között magánhálózati hálózatot hozzon össze.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Egyetlen csomópontos HSR a DR (Cost optimalizált) 
 
 Ez a topológia egyetlen SID-vel támogatja a Felskálázási konfiguráció egyik csomópontját, és a HANA rendszer replikálja a DR helyet az elsődleges biztonsági azonosítóhoz. A diagramon csak egyetlen SID rendszer látható az elsődleges helyen, de a többszörös SID-(MCOS-) rendszerek is támogatottak. A DR helyen a QA-példányhoz egy HLI egységet használ, miközben az elsődleges helyről futnak az üzemi műveletek. A DR feladatátvétel (vagy feladatátvételi teszt) során a rendszer leállítja a DR helyen található QA-példányt.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópontos HSR a DR (Cost optimalizált)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-hez | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-hez |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memória adatbázisának méretétől függ. Ha szeretné megtudni, hogy a memóriában hány adatbázis-méret támogatott a több SID-alapú környezetben, tekintse meg az [Áttekintés és az architektúra](./hana-overview-architecture.md)című témakört.
- A DR helyen: a kötetek és a csatlakoztatási pontok konfigurálva vannak (a Dr-helyen lévő "GYÁRTÁSIRENDELÉS-példányként" van megjelölve) a DR HLI egységben lévő éles HANA-példány telepítéséhez. 
- A DR helyen: a QA-példány telepítéséhez be kell állítani az adatokat, a naplók biztonsági mentését, a naplót és a közös köteteket a QA számára (a "QA példány telepítése" jelöléssel).
- Az elsődleges csomópont a DR csomóponttal szinkronizálódik a HANA rendszerreplikáció használatával. 
- A [Global REACH](../../../expressroute/expressroute-global-reach.md) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy a helyi hálózatok között magánhálózati hálózatot hozzon össze.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Magas rendelkezésre állás és vész-helyreállítás a HSR 
 
 Ez a topológia két csomópontot támogat a HANA rendszer replikációs konfigurációjához a helyi régiók magas rendelkezésre állása érdekében. A DR esetében a DR régió harmadik csomópontja a HSR (aszinkron mód) használatával szinkronizál az elsődleges hellyel. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Magas rendelkezésre állás és vész-helyreállítás a HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A DR helyen: a kötetek és a csatlakoztatási pontok konfigurálva vannak ("PROD DR-példányként" jelölve) a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- Az elsődleges hely csomópontja a DR csomóponttal szinkronizálódik a HANA rendszerreplikáció használatával. 
- A [Global REACH](../../../expressroute/expressroute-global-reach.md) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy a helyi hálózatok között magánhálózati hálózatot hozzon össze.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Magas rendelkezésre állás és vész-helyreállítás a HSR (Cost optimalizált)
 
 Ez a topológia két csomópontot támogat a HANA rendszer replikációs konfigurációjához a helyi régiók magas rendelkezésre állása esetén. A DR régióban a DR régió harmadik csomópontja a HSR (aszinkron mód) használatával szinkronizálja az elsődleges hellyel, míg egy másik példány (például a QA) már fut a DR csomópontból. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Magas rendelkezésre állás és vész-helyreállítás a HSR (Cost optimalizált)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-hez | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-hez |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A DR helyen: a kötetek és a csatlakoztatási pontok konfigurálva vannak ("PROD DR-példányként" jelölve) a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR helyen: a QA-példány telepítéséhez be kell állítani az adatokat, a naplók biztonsági mentését, a naplót és a közös köteteket a QA számára (a "QA példány telepítése" jelöléssel).
- Az elsődleges hely csomópontja a DR csomóponttal szinkronizálódik a HANA rendszerreplikáció használatával. 
- A [Global REACH](../../../expressroute/expressroute-global-reach.md) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy a helyi hálózatok között magánhálózati hálózatot hozzon össze.

## <a name="scale-out-with-dr-using-hsr"></a>Vertikális felskálázás DR használatával HSR
 
Ez a topológia több csomópontot támogat a kibővíthető DR-ben. Ezt a topológiát készenléti csomóponttal vagy anélkül is kérheti. Az elsődleges hely csomópontja a DR hely csomóponttal szinkronizálódik a HANA rendszerreplikáció (aszinkron mód) használatával.


### <a name="architecture-diagram"></a>Architektúradiagram  

[Vertikális ![ felskálázás Dr használatával HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| Hálózati adapter logikai interfésze | SKU típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópont-csomópont típusú kommunikáció |
| C | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópontok közötti |
| T | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo> | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo+2> | team0. Bérlő + 2 | Csomópont-csomópont típusú kommunikáció |
| C | II. TÍPUS | VLAN\<tenantNo+1> | team0. Bérlő + 1 | Csomópontok közötti |
| T | II. TÍPUS | VLAN\<tenantNo+3> | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatlakoztatási pontok előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/shared | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR csomóponton**|
|/hana/shared | HANA-telepítés üzemi SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-hez | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A DR helyen: a kötetek és a csatlakoztatási pontok a DR HLI egységben a termelési HANA-példány telepítéséhez vannak konfigurálva. 
- Az elsődleges hely csomópontja a DR csomóponttal szinkronizálódik a HANA rendszerreplikáció használatával. 
- A [Global REACH](../../../expressroute/expressroute-global-reach.md) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy a helyi hálózatok között magánhálózati hálózatot hozzon össze.


## <a name="next-steps"></a>Következő lépések
- [Infrastruktúra és kapcsolat](./hana-overview-infrastructure-connectivity.md) a HANA nagyméretű példányaihoz
- [Magas rendelkezésre állás és vész-helyreállítási](./hana-overview-high-availability-disaster-recovery.md) szolgáltatás nagyméretű HANA-példányokhoz
