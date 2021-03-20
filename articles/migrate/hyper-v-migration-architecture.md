---
title: Hogyan működik a Hyper-V áttelepítés Azure Migrate?
description: A Hyper-V áttelepítésének ismertetése Azure Migrate
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751019"
---
# <a name="how-does-hyper-v-replication-work"></a>Hogyan működik a Hyper-V replikáció?

Ez a cikk áttekintést nyújt a Hyper-V virtuális gépek Azure Migrate Server áttelepítési eszközzel történő áttelepítésekor használt architektúráról és folyamatokról.

[Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint a magán-és nyilvános Felhőbeli virtuális gépek felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.

## <a name="agentless-migration"></a>Ügynök nélküli áttelepítés

A Azure Migrate Server áttelepítési eszköz ügynök nélküli replikációt biztosít a helyszíni Hyper-V virtuális gépek számára a Hyper-V-re optimalizált áttelepítési munkafolyamat használatával. A szoftveres ügynököt csak Hyper-V-gazdagépekre vagy fürtcsomópontokre telepítheti. Semmit nem kell telepíteni a Hyper-V virtuális gépekre.

## <a name="server-migration-and-azure-site-recovery"></a>Kiszolgáló áttelepítése és Azure Site Recovery

A Azure Migrate kiszolgáló áttelepítése egy eszköz a helyszíni számítási feladatok és a felhőalapú virtuális gépek Azure-ba történő áttelepítéséhez. Site Recovery vész-helyreállítási eszköz. Az eszközök osztoznak az adatreplikáláshoz használt közös technológiai összetevőkben, de különböző célokra szolgálnak. 


## <a name="architectural-components"></a>Az architektúra összetevői

![A diagram egy olyan forrás Hyper-V hálózatot mutat be, amely a H T T P S adatcsatornán keresztül Microsoft Azure, és a táblázatban szereplő részleteket ismerteti.](./media/hyper-v-replication-architecture/architecture.png)



**Összetevő** | **Üzembe helyezés** | 
--- | --- 
**Replikációs szolgáltató** | A Microsoft Azure Site Recovery-szolgáltató a Hyper-V-gazdagépekre van telepítve, és regisztrálva van az Azure Migration Server áttelepítésével.<br/> A szolgáltató a Hyper-V virtuális gépek replikálását koordinálja.
**Recovery Services ügynök** | A Microsoft Azure Recovery szolgáltatás ügynöke kezeli az adatreplikációt. Együttműködik a szolgáltatóval, hogy a Hyper-V virtuális gépekről az Azure-ba replikálja az adatait.<br/> A replikált adatait a rendszer feltölti az Azure-előfizetésében lévő Storage-fiókba. A kiszolgáló áttelepítési eszköze a replikált adatfeldolgozást végez, és az előfizetésben lévő replika lemezekre alkalmazza. A replika lemezek az áttelepítés során az Azure-beli virtuális gépek létrehozásához használatosak.

- Az összetevőket egyetlen telepítési fájl telepíti, amely a portálon Azure Migrate-kiszolgáló áttelepítéséről tölthető le.
- A szolgáltató és berendezés a 443-as kimenő HTTPS-portot használja a Azure Migrate-kiszolgáló áttelepítésével való kommunikációhoz.
- A szolgáltató és az ügynök közötti kommunikáció biztonságos és titkosított.


## <a name="replication-process"></a>Replikációs folyamat

1. Ha engedélyezi a replikációt egy Hyper-V virtuális gép számára, a kezdeti replikálás megkezdődik.
2. Hyper-V virtuális gép pillanatképe készül.
3. A virtuális merevlemezek replikálása egyenként történik, amíg az összes Azure-ba nem másolódik. A kezdeti replikálási idő a virtuális gép méretétől és a hálózati sávszélességtől függ.
4. A kezdeti replikáció során végrehajtott lemezes változások a Hyper-V replikával vannak nyomon követve, és naplófájlokban (HRL-fájlokban) tárolódnak.
    - A naplófájlok ugyanabban a mappában találhatók, mint a lemezek.
    - Minden lemezhez tartozik egy társított HRL-fájl, amelyet a rendszer a másodlagos tárolóba továbbít.
    - A pillanatkép- és a naplófájlok a kezdeti replikáció végrehajtása közben is lemezerőforrásokat használnak.
4. A kezdeti replikálás befejeződése után a rendszer törli a virtuális gép pillanatképét, és megkezdi a különbözeti replikációt.
5. A növekményes lemezek változásai a HRL-fájlokban vannak nyomon követve. A replikációs naplókat a Recovery Services ügynök rendszeresen feltölti egy Azure Storage-fiókba.


## <a name="performance-and-scaling"></a>Teljesítmény és skálázás

A Hyper-V replikálási teljesítményét a virtuálisgép-méretet, a virtuális gépek adatváltozási sebességét (adatváltozási sebessége), a Hyper-V-gazdagépen rendelkezésre álló lemezterületet a naplófájlok tárolásához, a replikálási adatok sávszélességének feltöltéséhez és az Azure-beli cél tárolóhoz tartozó tényezők befolyásolják.

- Ha egyszerre több gépet replikál, használja a Hyper-V [Azure site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) a replikáció optimalizálásához.
- Tervezze meg a Hyper-V-replikációt, és ossza el az Azure Storage-fiókokon keresztüli replikálást a kapacitásnak megfelelően.

### <a name="control-upload-throughput"></a>A feltöltési sebesség szabályozása

A Hyper-V-gazdagépeken az adatok Azure-ba való feltöltéséhez használt sávszélesség korlátozható. légy óvatos. Ha túl alacsonyra állítja az értékeket, az negatív hatással lesz a replikációra, és késlelteti az áttelepítést.


1. Jelentkezzen be a Hyper-V-gazdagépre vagy-fürt csomópontjára.
2. A **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc** futtatásával nyissa meg a Windows Azure Backup MMC beépülő modult.
3. A beépülő modulban válassza a **Tulajdonságok módosítása** lehetőséget.
4. A **szabályozás** alatt jelölje be a **biztonsági mentési műveletek internetes sávszélesség-szabályozásának engedélyezése** jelölőnégyzetet. A munkamennyiség és a munkaidőn kívüli munkaidő korlátozásának beállítása. Az érvényes tartományok 512 kbps és 1 023 Mbps között vannak.
I

### <a name="influence-upload-efficiency"></a>A feltöltés hatékonyságának befolyásolása

Ha van tartalék sávszélesség a replikáláshoz, és szeretné megnövelni a feltöltéseket, növelheti a feltöltési feladathoz lefoglalt szálak számát a következőképpen:

1. Nyissa meg a beállításjegyzéket a regedit szolgáltatással.
2. Navigáljon a kulcs HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Növelje az adatfeltöltéshez használt szálak számát az egyes replikáló virtuális gépek esetében. Az alapértelmezett érték 4, a maximális érték 32. 




## <a name="next-steps"></a>Következő lépések

Próbálja ki a [Hyper-V áttelepítését](tutorial-migrate-hyper-v.md) Azure Migrate kiszolgáló áttelepítésével.
