---
title: Az Azure-beli SAP HANA architektúrája (nagyméretű példányok) | Microsoft Docs
description: A SAP HANA üzembe helyezésének architektúrája az Azure-ban (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45b84360c941e879461500a033ada3642fd5cbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671617"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>SAP HANA (nagyméretű példányok) architektúrája az Azure-ban

Magas szinten a SAP HANA az Azure-ban (nagyméretű példányok) megoldás rendelkezik a virtuális gépeken található SAP-alkalmazás réteggel. Az adatbázis-réteg az SAP TDI-konfigurált hardveren található, amely ugyanabban az Azure-régióban, amely az Azure IaaS csatlakozik, egy nagyméretű példány-bélyegzőn helyezkedik el.

> [!NOTE]
> Telepítse az SAP-alkalmazás réteget ugyanabban az Azure-régióban, mint az SAP adatbázis-kezelő rétegét. Ez a szabály jól dokumentálva van az Azure-beli SAP-munkaterhelésekről szóló közzétett információkban. 

Az Azure-beli SAP HANA általános architektúrája (nagyméretű példányok) egy SAP TDI-tanúsítvánnyal rendelkező hardver-konfigurációt biztosít, amely egy nem virtualizált, operációs rendszer nélküli, nagy teljesítményű kiszolgáló a SAP HANA-adatbázis számára. Emellett lehetővé teszi az Azure számára az erőforrások méretezését az SAP-alkalmazás rétegéhez, hogy megfeleljen az igényeinek.

![Az Azure-beli SAP HANA építészeti áttekintése (nagyméretű példányok)](./media/hana-overview-architecture/image1-architecture.png)

A bemutatott architektúra három szakaszra oszlik:

- **Right**: olyan helyszíni infrastruktúrát mutat be, amely különböző alkalmazásokat futtat az adatközpontokban, így a végfelhasználók hozzáférhetnek az üzletági alkalmazásokhoz, például az SAP-hoz. Ideális esetben ez a helyszíni infrastruktúra a [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-mel csatlakozik az Azure-hoz.

- **Központ**: megjeleníti az Azure IaaS, és ebben az esetben a virtuális gépeket az olyan SAP-vagy más alkalmazások üzemeltetésére használja, amelyek a SAP HANA adatbázis-kezelő rendszerként használják. Kisebb HANA-példányok, amelyek a virtuális gépek által biztosított memóriával működnek, az alkalmazás rétegével együtt. További információ a virtuális gépekről: [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/).

   Az Azure hálózati szolgáltatásai az SAP-rendszerek és más alkalmazások virtuális hálózatokra való csoportosítására szolgálnak. Ezek a virtuális hálózatok a helyszíni rendszerekhez kapcsolódnak, valamint az Azure-ban (nagyméretű példányokon) SAP HANA.

   Az Azure-ban futtatott SAP NetWeaver-alkalmazások és-adatbázisok esetében lásd: [SAP-támogatás megjegyzés #1928533 – SAP-alkalmazások az Azure-ban: támogatott termékek és Azure VM-típusok](https://launchpad.support.sap.com/#/notes/1928533). Az SAP-megoldások Azure-beli üzembe helyezésével kapcsolatos dokumentációért lásd:

  -  [Az SAP használata Windows rendszerű virtuális gépeken](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [SAP-megoldások használata Azure-beli virtuális gépeken](get-started.md)

- **Left**: a SAP HANA TDI-tanúsítvánnyal rendelkező hardvert jeleníti meg az Azure nagyméretű példányának bélyegében. A HANA nagyméretű példányok egységei az Azure-előfizetéshez tartozó virtuális hálózatokhoz kapcsolódnak, és ugyanazt a technológiát használják, mint a helyszíni kapcsolat az Azure-ba. A május 2019-es verziójától kezdve egy optimalizálás lett bevezetve, amely lehetővé teszi a HANA nagyméretű példány-egységek és az Azure-beli virtuális gépek közötti kommunikációt a ExpressRoute-átjáró bevonása nélkül. Ebben az architektúrában az ExpressRoute Fast Path nevű optimalizálás jelenik meg (piros vonal). 

Az Azure nagyméretű példányának bélyegzője maga ötvözi a következő összetevőket:

- **Számítástechnika**: azok a kiszolgálók, amelyek a szükséges számítástechnikai képességet biztosító Intel Xeon processzorok különböző generációján alapulnak, és SAP HANA minősítéssel rendelkeznek.
- **Hálózat**: egységes, nagy sebességű hálózati háló, amely összekapcsolódik a számítástechnikai, tárolási és LAN-összetevőkkel.
- **Storage**: egy egységes hálózati hálón keresztül elérhető tárolási infrastruktúra. A megadott tárolási kapacitás az Azure (nagyméretű példányok) telepített konfigurációjának adott SAP HANA függ. A tárterület további kapacitása további havi díj ellenében vehető igénybe.

A nagyméretű példányok Stamp több-bérlős infrastruktúráján belül az ügyfelek elkülönített bérlőként települnek. A bérlő üzembe helyezése során egy Azure-előfizetést kell megkeresnie az Azure-regisztráción belül. Ezt az Azure-előfizetést a HANA nagyméretű példány számlázása képezi. Ezek a bérlők 1:1 kapcsolattal rendelkeznek az Azure-előfizetéssel. A hálózat esetében lehetséges, hogy egy, a különböző Azure-előfizetésekhez tartozó különböző virtuális hálózatokból egy adott bérlőn üzembe helyezett HANA nagyméretű példány-egységhez fér hozzá. Ezeknek az Azure-előfizetéseknek ugyanahhoz az Azure-regisztrációhoz kell tartozniuk. 

A virtuális gépekhez hasonlóan a SAP HANA az Azure-ban (nagyméretű példányok) több Azure-régióban is elérhető. A vész-helyreállítási képességek biztosításához dönthet úgy, hogy bekapcsolja. Egy geo-politikai régió különböző nagyméretű példányai egymáshoz kapcsolódnak. Például az USA nyugati régiójában és az USA keleti régiójában lévő HANA Large instance Stamps egy dedikált hálózati kapcsolaton keresztül csatlakozik a vész-helyreállítási replikáláshoz. 

Ugyanúgy, ahogy a különböző virtuálisgép-típusok közül választhat az Azure Virtual Machines segítségével, különböző, a SAP HANA különböző számítási feladatokhoz igazított SKU-ból választhat. Az SAP a memória és a processzor közötti arányt alkalmazza a különböző számítási feladatokhoz az Intel processzor generációi alapján. A következő táblázat a rendelkezésre álló SKU-típusokat tartalmazza.

[A HLI](hana-available-skus.md)számára elérhető SKU-ket találhat.

**Következő lépések**
- [SAP HANA (nagyméretű példányok) hálózati architektúrájának](hana-network-architecture.md) átirányítása
