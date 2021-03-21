---
title: SAP HANA rendelkezésre állás Azure-régiók között | Microsoft Docs
description: A rendelkezésre állási megfontolások áttekintése, ha több Azure-régióban található Azure-beli virtuális gépeken SAP HANA futtat.
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
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f925e72493a94bcdbde64bdfbcef6acf83977f52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669723"
---
# <a name="sap-hana-availability-across-azure-regions"></a>SAP HANA rendelkezésre állás Azure-régiók között

Ez a cikk a különböző Azure-régiók SAP HANA rendelkezésre állásával kapcsolatos forgatókönyveket ismerteti. Az Azure-régiók közötti távolság miatt a SAP HANA rendelkezésre állása több Azure-régióban is különleges szempontokat foglal magában.

## <a name="why-deploy-across-multiple-azure-regions"></a>Miért érdemes több Azure-régiót üzembe helyezni

Az Azure-régiókat gyakran nagy távolságok választják el egymástól. A geopolitikai régiótól függően az Azure-régiók közötti távolság több száz mérfölddel vagy akár több ezer mérföld is lehet, például a Egyesült Államok. A távolság miatt a két különböző Azure-régióban üzembe helyezett eszközök közötti hálózati forgalom jelentős hálózati továbbítási késést tapasztal. A késés elég jelentős ahhoz, hogy kizárja a szinkron adatcserét két SAP HANA példány között a tipikus SAP-munkaterhelések alatt. 

Másfelől a szervezeteknek gyakran van távolsági követelményük az elsődleges adatközpont és a másodlagos Adatközpont helye között. A távolsági követelmények segítenek biztosítani a rendelkezésre állást, ha a természeti katasztrófák szélesebb földrajzi helyen történnek. Ilyenek például a Karib-szigetek és a Florida a 2017-es szeptemberben és októberben sújtott hurrikánok. Előfordulhat, hogy a szervezete legalább minimális távolságra vonatkozó követelményt tartalmaz. A legtöbb Azure-ügyfél esetében a minimális távolság meghatározása megköveteli az [Azure-régiók](https://azure.microsoft.com/regions/)közötti rendelkezésre állás kialakítását. Mivel a két Azure-régió közötti távolság túl nagy a HANA szinkron replikációs mód használatához, a RTO és a RPO követelmények arra kényszerítheti, hogy a rendelkezésre állási konfigurációkat egy adott régióban telepítse, majd kiegészítse a további üzemelő példányokkal egy második régióban.

Az ebben a forgatókönyvben megfontolandó másik szempont a feladatátvétel és az ügyfél-átirányítás. Feltételezi, hogy a két különböző Azure-régióban található SAP HANA-példányok közötti feladatátvétel mindig kézi feladatátvétel. Mivel SAP HANA rendszer-replikálás replikációs módja aszinkron értékre van beállítva, lehetséges, hogy az elsődleges HANA-példányon véglegesített adatmennyiség még nem tette meg a másodlagos HANA-példányra. Az automatikus feladatátvétel ezért nem olyan konfigurációknál használható, ahol a replikálás aszinkron. A manuálisan vezérelt feladatátvételsel, ahogy a feladatátvételi gyakorlat esetében is, intézkedéseket kell meghoznia annak érdekében, hogy az elsődleges oldalon lévő összes véglegesített adat a másodlagos példányra kerüljön, mielőtt manuálisan áthelyezi a másik Azure-régióba.
 
Az Azure Virtual Network eltérő IP-címtartományt használ. Az IP-címek üzembe helyezése a második Azure-régióban történik. Ezért vagy módosítania kell a SAP HANA-ügyfél konfigurációját, vagy lehetőleg a névfeloldás módosításához lépéseket kell létrehoznia. Így a rendszer átirányítja az ügyfeleket az új másodlagos hely kiszolgálói IP-címére. További információ: az SAP-cikk [ügyfélkapcsolatának helyreállítása az átvétel után](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Egyszerű rendelkezésre állás két Azure-régió között

Dönthet úgy, hogy egyetlen régión belül nem helyezi el a rendelkezésre állási konfigurációt, de az is szükséges, hogy a számítási feladatok elvégzése vészhelyzet esetén is megtörténjen. Ilyen forgatókönyvek esetében a nem termékrendszer-rendszerek jellemzőek. Bár a rendszer leállt a fél napra, vagy akár egy napig is fenntartható, nem engedélyezheti, hogy a rendszer 48 órán keresztül elérhetetlenné váljon. Ahhoz, hogy a telepítőt olcsóbban lehessen elkészíteni, futtasson egy másik olyan rendszer, amely még kevésbé fontos a virtuális gépen. A másik rendszer rendeltetési helyként működik. A virtuális gépet a másodlagos régióban is kisebbre lehet méretezni, és nem kell betöltést választania. Mivel a feladatátvétel manuális, és számos további lépést tesz szükségessé a teljes alkalmazás-verem feladatátvételéhez, a virtuális gép leállításához szükséges további időt, az átméretezést, majd a virtuális gép újraindítását is elfogadható.

Ha a DR célját egy virtuális gépen található QA-rendszerrel közösen használja, a következő szempontokat kell figyelembe vennie:

- Két [működési mód](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) van delta_datashipping-és logreplay, amelyek egy ilyen forgatókönyvhöz érhetők el
- Mindkét működési mód esetében eltérő memória-követelmények vannak az előzetes betöltés nélkül
- Előfordulhat, hogy a Delta_datashipping a logreplay megkövetelése nélkül drasztikusan kevesebb memóriát igényel. A [SAP HANA rendszer-replikálásának végrehajtásához](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf) tekintse meg az SAP-dokumentum 4,3. fejezetét.
- A logreplay-üzemeltetési üzemmód nem tölthető be a determinisztikus, és a betöltött oszlopcentrikus-struktúráktól függ. Szélsőséges esetekben az elsődleges példány memóriájának 50%-át is megkövetelheti. A logreplay működési mód memóriája független, függetlenül attól, hogy beállította-e az előre betöltött adatmennyiséget.


![Két virtuális gép diagramja két régióban](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Ebben a konfigurációban nem adhat meg RPO = 0 értéket, mert a HANA rendszer replikációs módja aszinkron. Ha RPO = 0 értéket kell megadnia, ez a konfiguráció nem a választott konfiguráció.

Előfordulhat, hogy a konfigurációban elvégezhető kis módosítás az, ha előre betöltésként konfigurálja az adathalmazt. Azonban a feladatátvétel manuális jellege és az, hogy az alkalmazás rétegeinek is át kell térniük a második régióra, előfordulhat, hogy az adatátvitelt nem érdemes megfontolni. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>A rendelkezésre állás egyesítése egy régión belül és régiók között 

A régión belüli és közötti rendelkezésre állási kombinációt a következő tényezők okozhatják:

- Az Azure-régión belüli RPO = 0 követelmény.
- A szervezet nem hajlandó vagy nem tud olyan globális műveleteket gyakorolni, amely egy nagyobb régiót érintő jelentős természeti katasztrófát érint. Az elmúlt néhány évben a Karib-szigetet elérő hurrikánok esetében ez volt a helyzet.
- Az elsődleges és másodlagos helyek közötti távolságot igénylő rendeletek, amelyek egyértelműen az Azure rendelkezésre állási zónái által biztosítottak.

Ezekben az esetekben beállíthatja, hogy milyen SAP-hívások [SAP HANA többszintű rendszerreplikálási konfigurációt](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) a HANA rendszerreplikáció használatával. Az architektúra így néz ki:

![Három virtuális gép diagramja két régióban](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Az SAP bevezette a többhelyes [rendszer replikálását](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) a HANA 2,0 SPS3. A többhelyes rendszerreplikáció bizonyos előnyökkel jár a frissítési forgatókönyvekben. Például a DR helyet (2. régió) nem érinti a rendszer, ha a másodlagos, HA a hely karbantartás vagy frissítés miatt nem érhető el. További információ a HANA többhelyes rendszerek replikálásáról [itt](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html)található.
A több cél replikálással rendelkező lehetséges architektúra a következőképpen néz ki:

![Három virtuális gép ábrázolása két régióban Milti – cél](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Ha a szervezet a második (DR) Azure-régióban magas rendelkezésre állási készültségi követelményekkel rendelkezik, akkor az architektúra a következőképpen fog kinézni:

![Diagram, amely a második (DR) Azure-régióban magas rendelkezésre állási készültségi követelményekkel rendelkező szervezetet mutat be.](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


A logreplay as Operations mód használata esetén ez a konfiguráció egy alacsony RTO rendelkező RPO = 0 értéket biztosít az elsődleges régión belül. A konfiguráció emellett a tisztességes RPO is biztosítja, ha a második régióba való áttérés is részt vesz. A második régió RTO időpontja attól függ, hogy az adatkészlet előre be van-e töltve. Számos ügyfél használja a másodlagos régióban található virtuális gépet egy tesztelési rendszer futtatásához. Ebben az esetben az adathalmaz nem tölthető be előre.

> [!IMPORTANT]
> A különböző rétegek közötti műveleti módoknak homogénnek kell lenniük. Az 1. és a 2. szinten **nem** használhatja a logreply, és a 3. szintű delta_datashipping. Az összes réteghez konzisztensnek kell lennie, vagy más működési módot kell választania. Mivel a delta_datashipping nem alkalmas a RPO = 0 érték megadására, a többrétegű konfigurációk esetében az egyetlen ésszerű működési mód marad a logreplay. A működési módokkal és bizonyos korlátozásokkal kapcsolatos részletekért tekintse meg az SAP-cikkek [működési módjait SAP HANA rendszer-replikáláshoz](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)című témakört. 

## <a name="next-steps"></a>Következő lépések

A konfigurációk Azure-beli beállításával kapcsolatos részletes útmutatásért lásd:

- [Rendszerreplikáció beállítása SAP HANA Azure-beli virtuális gépeken](sap-hana-high-availability.md)
- [SAP HANA magas rendelkezésre állása rendszerreplikáció használatával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
