---
title: Az Azure-beli SAP HANA áttekintése (nagyméretű példányok) | Microsoft Docs
description: A SAP HANA üzembe helyezésének áttekintése az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c1cd4e7d65897634b5a8a8fa8be46275bbd4b88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676865"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Mi az SAP HANA az Azure-ban (nagy méretű példányok)?

A SAP HANA az Azure-ban (nagyméretű példányok) egyedülálló megoldás az Azure-ra. Amellett, hogy virtuális gépeket biztosít a SAP HANA üzembe helyezéséhez és futtatásához, az Azure lehetőséget biztosít az Ön számára dedikált, operációs rendszer nélküli kiszolgálókon történő SAP HANA futtatására és telepítésére. Az Azure-beli (nagyméretű példányok) megoldás SAP HANA az Önhöz rendelt, nem megosztott gazdagépre vagy kiszolgálóra épülő hardverre épít. A kiszolgáló hardvere nagyobb bélyegbe van ágyazva, amely számítási/kiszolgálói, hálózatkezelési és tárolási infrastruktúrát tartalmaz. A SAP HANA az Azure-ban (nagyméretű példányok) különböző kiszolgálói SKU-ket vagy méreteket biztosít. Az egységek 36 Intel CPU-maggal és 768 GB memóriával rendelkezhetnek, és akár 480 Intel CPU-maggal rendelkező, akár 24 TB memóriát tartalmazó egységekre is felmehetnek.

Az infrastruktúra-bélyegzőn belüli ügyfél-elkülönítést a bérlők végzik, amelyek a következőképpen néznek ki:

- **Hálózatkezelés**: az infrastruktúra-veremben lévő ügyfelek elkülönítése az ügyfél által hozzárendelt bérlői virtuális hálózatok használatával. A bérlő egyetlen ügyfélhez van rendelve. Egy ügyfél több Bérlővel is rendelkezhet. A bérlők hálózati elkülönítése tiltja a bérlők közötti hálózati kommunikációt az infrastruktúra-Stamp szintjén, még akkor is, ha a bérlők ugyanahhoz az ügyfélhez tartoznak.
- **Tárolási összetevők**: a tároló virtuális gépeken keresztüli elkülönítés, amelyekhez tárolási kötetek vannak rendelve. A tárolási kötetek csak egy tároló virtuális géphez rendelhetők hozzá. A tárolási virtuális gépek kizárólag egyetlen bérlőhöz vannak hozzárendelve az infrastruktúra-veremben. Ennek eredményeképpen a tároló virtuális géphez rendelt tárolási kötetek csak egy adott és kapcsolódó bérlőhöz érhetők el. Nem láthatók a különböző telepített bérlők között.
- **Kiszolgáló vagy gazdagép**: nem oszlik meg A kiszolgálók vagy A gazdagépek az ügyfelek vagy a bérlők között. Egy ügyfélre telepített kiszolgáló vagy gazdagép egy olyan atomi operációs rendszer nélküli számítási egység, amely egyetlen bérlőhöz van rendelve. *Nincs* szükség hardver particionálásra vagy lágy particionálásra, ami a gazdagép vagy a kiszolgáló másik ügyféllel való megosztását eredményezheti. Az adott bérlő tároló virtuális géphez hozzárendelt tárolási kötetek egy ilyen kiszolgálóhoz vannak csatlakoztatva. A bérlők egy vagy több, kizárólag a különböző SKU-ban hozzárendelt kiszolgálói egységhez tartozhatnak.
- Az Azure-beli (nagyméretű példányok) infrastruktúra-bélyegzőn belüli SAP HANA számos különböző bérlőt helyezünk üzembe és különítenek el egymástól a bérlői fogalmakkal a hálózatkezelés, a tárolás és a számítási szint alapján. 


Ezek az operációs rendszer nélküli kiszolgálói egységek csak SAP HANA futtatására használhatók. A virtuális gépeken futó SAP-alkalmazás rétege vagy munkaterhelése. Az Azure-ban (nagyméretű példányok) lévő SAP HANAt futtató infrastruktúra-bélyegek az Azure hálózati szolgáltatások gerincéhez csatlakoznak. Így az Azure-beli (nagyméretű példányok) egységek és a virtuális gépek közötti SAP HANA kis késleltetésű kapcsolat van megadva.

Január 2021-én megkülönböztetjük a HANA nagy példányszámú bélyegek két különböző változatát és az üzemelő példányok helyét:

- "3. változat" (Rev 3): azok a bélyegek, amelyeket az ügyfél számára elérhetővé tettek a 2019 júliusa előtt.
- "4. változat" (Rev 4): új Stamp-kialakítás, amely az Azure-beli virtuálisgép-gazdagépek közelében van üzembe helyezve, és amely eddig a következő Azure-régiókban jelent meg:
    -  USA 2. nyugati régiója 
    -  USA keleti régiója
    -  Kelet-RÉGIÓJA (két Availability Zones között)
    -  USA déli középső régiója (két Availability Zones)
    -  Nyugat-Európa
    -  Észak-Európa


Ez a dokumentum az Azure-ban (nagyméretű példányok) SAP HANAt lefedő dokumentumok egyike. Ez a dokumentum bemutatja a megoldás által nyújtott alapszintű architektúrát, felelősségeket és szolgáltatásokat. A megoldás magas szintű képességeit is tárgyaljuk. A legtöbb más terület, például a Hálózatkezelés és a kapcsolatok esetében négy további dokumentum részletezi és részletezi az információkat. Az Azure-beli SAP HANA dokumentációja (nagyméretű példányok) nem fedi le a virtuális gépeken futó SAP NetWeaver telepítésének vagy központi telepítésének aspektusait. Az Azure-beli SAP NetWeaver az azonos Azure-beli dokumentációs tárolóban található külön dokumentumokban szerepel. 


A HANA nagyméretű példányok különböző dokumentumai a következő területeket fedik le:

- [SAP HANA (nagyméretű példányok) áttekintése és architektúrája az Azure-ban](hana-overview-architecture.md)
- [SAP HANA (nagyméretű példányok) infrastruktúrája és kapcsolódás az Azure-ban](hana-overview-infrastructure-connectivity.md)
- [SAP HANA (nagyméretű példányok) telepítése és konfigurálása az Azure-ban](hana-installation.md)
- [SAP HANA (nagyméretű példányok) magas rendelkezésre állása és vész-helyreállítás az Azure-ban](hana-overview-high-availability-disaster-recovery.md)
- [SAP HANA (nagyméretű példányok) hibaelhárítás és monitorozás az Azure-ban](troubleshooting-monitoring.md)
- [Magas rendelkezésre állás beállítása a SUSE-ben a STONITH használatával](./ha-setup-with-stonith.md)
- [Az operációs rendszer biztonsági mentése és visszaállítása a 3. típusú bélyegzők II. típusához](./os-backup-type-ii-skus.md)
- [Megtakarítás a SAP HANA nagyméretű példányaival Azure-foglalás segítségével](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**Következő lépések**
- Tekintse át [a feltételeket](hana-know-terms.md)
