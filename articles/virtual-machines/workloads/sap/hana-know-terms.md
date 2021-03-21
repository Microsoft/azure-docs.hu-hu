---
title: A SAP HANA feltételeinek ismerete az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: Ismerje meg az Azure-beli SAP HANA használati feltételeit (nagyméretű példányok).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92ef2e59dab1921eae8e7d88249e75116601c597
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670872"
---
# <a name="know-the-terms"></a>A feltételek megismerése

Számos gyakori definíciót széles körben használnak az architektúra és a technikai üzembe helyezési útmutatóban. Vegye figyelembe a következő kifejezéseket és azok jelentéseit:

- **IaaS**: infrastruktúra-szolgáltatás.
- **Péter**: szolgáltatásként szolgáló platform.
- **SaaS**: szolgáltatott szoftver.
- **SAP-összetevő**: egy egyéni SAP-alkalmazás, például az ERP központi összetevő (ECC), az üzleti raktár (BW), a Solution Manager vagy a Enterprise Portal (EP). Az SAP-összetevők a hagyományos ABAP vagy Java-technológiákon, vagy egy nem NetWeaver-alapú alkalmazáson, például üzleti objektumokon alapulnak.
- **SAP-környezet**: egy vagy több olyan SAP-összetevő, amely logikailag csoportosított üzleti funkciók végrehajtására, például fejlesztésre, minőségbiztosításra, képzésre, vész-helyreállításra vagy éles üzemre.
- **SAP-környezet**: az informatikai környezet teljes SAP-eszközeire utal. Az SAP-környezet az összes éles és nem éles környezetet magában foglalja.
- **SAP-rendszer**: az adatbázis-kezelő réteg és az alkalmazás rétegének kombinációja, például egy SAP ERP fejlesztői rendszer, egy SAP BW tesztelési rendszer és egy SAP CRM termelési rendszer. Az Azure-beli üzemelő példányok nem támogatják a két réteg a helyszíni és az Azure közötti felosztását. Az SAP rendszer üzembe helyezése a helyszínen történik, vagy az Azure-ban van üzembe helyezve. Az SAP-környezetek különböző rendszereit üzembe helyezheti az Azure-ban vagy a helyszínen egyaránt. Telepítheti például az SAP CRM fejlesztési és tesztelési rendszereit az Azure-ban, miközben üzembe helyezi az SAP CRM üzemi rendszerét a helyszínen. Az Azure-beli SAP HANA (nagyméretű példányok) esetében a rendszer azt a célt szolgálja, hogy a virtuális gépek SAP-alkalmazási rétegét, illetve az Azure-beli (nagyméretű példányok) bélyegző SAP HANA egy egységéhez tartozó SAP HANA-példányt üzemelteti.
- **Nagyméretű példány bélyegzője**: olyan hardveres infrastruktúra-verem, amely SAP HANA TDI-tanúsítvánnyal rendelkezik, és dedikáltan SAP HANA példányok futtatására az Azure-ban.
- **SAP HANA az Azure-ban (nagyméretű példányok):** Az ajánlat hivatalos neve az Azure-ban, hogy HANA-példányokat futtasson SAP HANA TDI-tanúsítvánnyal rendelkező hardvereken, amelyeket nagyméretű példányokban helyeztek üzembe különböző Azure-régiókban. A *HANA nagyméretű példány* kapcsolódó kifejezése rövid SAP HANA az Azure-ban *(nagyméretű példányok)* , és széles körben használatos ebben a technikai üzembe helyezési útmutatóban.
- **Létesítmények** közötti: olyan forgatókönyv, amelyben a virtuális gépeket egy olyan Azure-előfizetéshez telepítik, amely helyek közötti, többhelyes vagy Azure ExpressRoute kapcsolatot tartalmaz a helyszíni adatközpontok és az Azure között. A közös Azure-dokumentációban az ilyen típusú központi telepítések a létesítmények közötti forgatókönyvekben is szerepelnek. A kapcsolódás oka a helyszíni tartományok, a helyszíni Azure Active Directory/OpenLDAP és a helyszíni DNS kiterjesztése az Azure-ba. A helyszíni környezet az Azure-előfizetések Azure-eszközeire van kiterjesztve. Ezzel a bővítménnyel a virtuális gépek a helyszíni tartomány részei lehetnek. 

   A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és futtathatnak szolgáltatásokat ezeken a virtuális gépeken (például az adatbázis-kezelő szolgáltatásokban). A helyszíni és az Azure által üzembe helyezett virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a forgatókönyv a legtöbb SAP-eszköz üzembe helyezésének módjára jellemző. További információ: [Azure VPN Gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) és [virtuális hálózat létrehozása helyek közötti kapcsolattal a Azure Portal használatával](../../../vpn-gateway/tutorial-site-to-site-portal.md).
- **Bérlő**: a HANA nagyméretű példány bélyegén üzembe helyezett ügyfelek elkülönítettek a *bérlőhöz.* A bérlő a többi bérlő hálózati, tárolási és számítási rétegében el van különítve. A különböző bérlők számára hozzárendelt tárolási és számítási egységek nem látják egymást, és nem tudnak kommunikálni egymással a HANA nagyméretű példány-bélyegző szintjén. Egy ügyfél dönthet úgy is, hogy a központi telepítések különböző bérlők között vannak. A HANA nagyméretű példány Stamp szintjén azonban nincs kommunikáció a bérlők között.
- **SKU-kategória**: a HANA Large-példány esetében az alábbi két SKU-kategóriát ajánljuk:
    - **I. típusú osztály**: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 és S224m
    - **Type II osztály**: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm és S960m
- **Stamp**: a Microsoft belső telepítési méretének meghatározása a HANA nagyméretű példányain. A HANA nagyméretű példányok üzembe helyezése előtt olyan HANA nagyméretű példány bélyegzőt kell üzembe helyezni, amely a számítási, hálózati és tárolási állványokból áll. Az ilyen üzembe helyezést HANA nagyméretű példány bélyegzőnek vagy a 4. változatnak (lásd alább) a **nagyméretű példányok** időszakának helyettesítőjét használjuk.
- **Változat**: két különböző Stamp-változat létezik a HANA nagyméretű példányainak bélyegzői esetében. Ezek különböznek az Azure-beli virtuálisgép-gazdagépek architektúrájában és közelségében
    - "3. változat" (Rev 3): az eredeti kialakítás, amely a 2016-as év közepétől lett üzembe helyezve.
    - "4. változat" (Rev 4): egy olyan új kialakítás, amely az Azure-beli virtuális gépek gazdagépei számára nagyobb közelséget biztosít, valamint az Azure-beli és a HANA nagyméretű példány-egységek közötti alacsonyabb hálózati késést. 
    - "Változat 4,2" (Rev 4,2): a meglévő 4. változatú tartományvezérlők esetében az erőforrások a BareMetal-infrastruktúrába kerülnek.  Az ügyfelek BareMetal-példányként férhetnek hozzá az erőforrásokhoz a Azure Portal. 

A felhőben számos további erőforrás érhető el a SAP-munkaterhelések üzembe helyezéséhez. Ha SAP HANA Azure-beli üzembe helyezését tervezi, akkor az Azure IaaS alapelveivel és az Azure IaaS SAP-számítási feladatainak üzembe helyezésével foglalkoznia kell. A folytatás előtt tekintse [meg az SAP-megoldások használata az Azure Virtual Machines](get-started.md) szolgáltatásban című témakört. 

**Következő lépések**
- [HLI-minősítés](hana-certification.md) megtekintése