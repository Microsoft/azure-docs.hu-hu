---
title: Zónák és zónák vészhelyreállításának engedélyezése az Azure Virtual Machines
description: Ez a cikk azt ismerteti, hogy mikor és hogyan használható az Azure-beli virtuális gépek zóna–zóna vészhelyreállítása.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713407"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Azure-beli virtuális gépek rendelkezésre állási zónák közötti vészhelyreállításának engedélyezése

Ez a cikk bemutatja, hogyan replikálhat, feladatátvételt és feladat-visszavételt azure-beli virtuális gépeket egy rendelkezésre állási zónából egy másikba, ugyanabban az Azure-régióban.

>[!NOTE]
>
>- A zóna–zóna vészhelyreállítás támogatása jelenleg a következő régiókra korlátozódik: Délkelet-Ázsia, Kelet-Japán, Kelet-Ausztrália, Nyugat-India JIO, Egyesült Királyság déli régiója, Nyugat-Európa, Észak-Európa, USA középső régiója, USA keleti régiója, USA 2. keleti régiója és USA 2. nyugati régiója.  
>- Site Recovery nem helyez át vagy tárol ügyféladatokat az üzembe helyezési régióból, ha az ügyfél zónák és zónák között vészhelyreállítást használ. Ha úgy döntenek, az ügyfelek egy másik régióból választhatnak recovery services-tárolót. A Recovery Services-tároló metaadatokat tartalmaz, tényleges ügyféladatokat azonban nem.

Site Recovery szolgáltatás úgy járul hozzá az üzletmenet-folytonossági és vészhelyreállítási stratégiához, hogy az üzleti alkalmazásokat a tervezett és nem tervezett leállások során is működésben tartja. Regionális kimaradás esetén ez az ajánlott vészhelyreállítási lehetőség az alkalmazások futtatására.

A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden zóna egy vagy több adatközponttal rendelkezik. 

Ha a virtuális gépeket egy másik régióban található rendelkezésre állási zónába szeretné áthelyezni, tekintse át ezt a [cikket.](../resource-mover/move-region-availability-zone.md)

## <a name="using-availability-zones-for-disaster-recovery"></a>Az Availability Zones használata vészhelyreállításhoz 

Az Availability Zones a virtuális gépek magas rendelkezésre állású konfigurációban való üzembe helyezésére használatosak. Előfordulhat, hogy túl közel vannak egymáshoz, hogy természeti katasztrófa esetén vészhelyreállítási megoldásként szolgáljanak.

Bizonyos esetekben azonban az Availability Zones vészhelyreállításhoz is használhatók:

- Sok olyan ügyfél, aki egy vészhelyreállítási stratégiával és az alkalmazásoknak a helyen való üzemeltetése során is volt, olykor az alkalmazások Azure-ba való áttelepítése után is ezt a stratégiát kell utánoznia. Ezek az ügyfelek tudomásul veszik, hogy a vészhelyreállítási stratégia nagy léptékű fizikai katasztrófa esetén nem fog működni, és elfogadja ezt a kockázatot. Az ilyen ügyfelek esetében a zónák és zónák vészhelyreállítása használható vészhelyreállítási lehetőségként.

- Sok más ügyfél bonyolult hálózati infrastruktúrával is jár, és a kapcsolódó költségek és összetettség miatt nem szeretné újból létrehozni egy másodlagos régióban. A zónák közötti vészhelyreállítás csökkenti az összetettséget, mivel redundáns hálózati fogalmakat Availability Zones a konfigurációt egyszerűbbé teszi. Az ilyen ügyfelek az egyszerűséget részesítik előnyben, és vészhelyreállítási Availability Zones is használhatók.

- Egyes régiókban, ahol nincs párosított régió ugyanabban a jogi joghatóságban (például Délkelet-Ázsia), a zónák közötti vészhelyreállítás tulajdonképpen vészhelyreállítási megoldásként szolgálhat, mivel segít biztosítani a jogi megfelelőséget, mivel az alkalmazások és az adatok nem lépnek át az országos határokon. 

- A zónák közötti vészhelyreállítás azt jelenti, hogy az Azure-beli vészhelyreállítással összehasonlítva rövidebb távolságban replikálja az adatokat, ezért kisebb késést és ezáltal alacsonyabb RPO-t láthat.

Bár ezek jelentős előnyök, fennáll a lehetőség, hogy a zónák és zónák vészhelyreállítása a teljes régióra kiterjedő természeti katasztrófa esetén nem állja meg a rugalmassági követelményeket.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Hálózatok zónák és zónák vészhelyreállításához

Ahogy korábban említettük, a zónák közötti vészhelyreállítás csökkenti az összetettséget, mivel redundáns hálózati fogalmakat Availability Zones a konfigurációt egyszerűbbé teszi. A zóna–zóna vészhelyreállítás forgatókönyv hálózati összetevőinek viselkedését az alábbiakban ismertetjük: 

- Virtual Network: A tényleges feladatátvételhez használhatja ugyanazt a virtuális hálózatot, mint a forráshálózat. A feladatátvételi tesztekhez a forrás virtuális hálózattól eltérő virtuális hálózatot használjon.

- Alhálózat: Az azonos alhálózatra történő feladatátvétel támogatott.

- Magánhálózati IP-cím: Ha statikus IP-címeket használ, ugyanezeket az IP-címeket használhatja a célzónában is, ha úgy dönt, hogy ezeket ilyen módon konfigurálja.

- Gyorsított hálózat: Az Azure-hoz és az Azure-beli vészhelyreállításhoz hasonlóan engedélyezheti a gyorsított hálózatépítést, ha a virtuális gép termékváltozata támogatja azt.

- Nyilvános IP-cím: A korábban létrehozott standard nyilvános IP-címet ugyanabban a régióban csatolhatja a cél virtuális géphez. Az alapszintű nyilvános IP-címek nem támogatják a rendelkezésre állási zónákkal kapcsolatos forgatókönyveket.

- Terheléselosztás: A standard terheléselosztás egy regionális erőforrás, ezért a cél virtuális gép csatolható ugyanannak a terheléselosztásnak a háttérkészletével. Új terheléselosztásra nincs szükség.

- Hálózati biztonsági csoport: Használhatja a forrás virtuális gépre alkalmazott hálózati biztonsági csoportokat.

## <a name="pre-requisites"></a>Előfeltételek

Mielőtt zónák közötti vészhelyreállítást helyez üzembe a virtuális gépeken, fontos biztosítani, hogy a virtuális gépen engedélyezett egyéb funkciók együttműködnek a zónák közötti vészhelyreállítással.

|Szolgáltatás  | Támogatási nyilatkozat  |
|---------|---------|
|A klasszikus virtuális gépeket   |     Nem támogatott    |
|ARM virtuális gépek    |    Támogatott    |
|Azure Disk Encryption v1 (kettős pass, Azure Active Directory (Azure AD))     |     Támogatott   |
|Azure Disk Encryption v2 (egy pass, Azure AD nélkül)    |    Támogatott    |
|Nem felügyelt lemezek    |    Nem támogatott    |
|Felügyelt lemezek    |    Támogatott    |
|Felhasználó által kezelt kulcsok    |    Támogatott    |
|Közelségi elhelyezési csoportok    |    Támogatott    |
|Biztonsági másolatok együttműködési képessége    |    A fájlszintű biztonsági mentés és visszaállítás támogatott. Lemez- és virtuálisgép-szintű biztonsági mentés és visszaállítás, és nem támogatott.    |
|Gyors hozzáadás/eltávolítás    |    A zónák közötti replikáció engedélyezése után lemezek is hozzáadhatóak. A zónák közötti replikáció engedélyezése után a lemezek eltávolítása nem támogatott.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Zónák és Site Recovery vészhelyreállítás beállítása

### <a name="log-in"></a>Bejelentkezés

Jelentkezzen be az Azure Portalra.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>A zónabeli Azure-beli virtuális gép replikációjának engedélyezése

1. A Azure Portal válassza a Virtuális gépek lehetőséget, vagy keresse meg és válassza a Virtuális gépek lehetőséget bármely oldalon. Válassza ki a replikálni kívánt virtuális gépet. A zónák és zónák vészhelyreállításához a virtuális gépnek már rendelkezésre állási zónában kell lennie.

2. A Műveletek részen válassza a Vészhelyreállítás elemet.

3. Ahogy az alábbi képen is látható, az Alapvető beállítások lapon válassza az "Igen" lehetőséget a vészhelyreállítás két Availability Zones?

    ![Alapszintű beállítások oldal](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Ha elfogadja az összes alapértelmezett beállítást, kattintson az Áttekintés + Replikáció kezdete, majd a Replikáció kezdete elemre.

5. Ha módosítani szeretné a replikációs beállításokat, kattintson a "Tovább: Speciális beállítások" elemre.

6. Ha szükséges, módosítsa a beállításokat az alapértelmezetttől távolról. Az Azure–Azure vészhelyreállítás felhasználói számára ismerősnek tűnhet ez az oldal. A panelen megjelenő lehetőségekkel kapcsolatos további részleteket itt [talál](./azure-to-azure-tutorial-enable-replication.md)

    ![Speciális beállítások oldal](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Kattintson a "Tovább: Áttekintés + Replikáció kezdete" gombra, majd a Replikáció kezdete elemre.

## <a name="faqs"></a>Gyakori kérdések

**1. Hogyan működik a díjszabás a zónák és zónák vészhelyreállítása esetén?**
A zónák és zónák vészhelyreállításának díjszabása megegyezik az Azure–Azure vészhelyreállítás díjszabásának. További részleteket a díjszabási oldalon talál [itt](https://azure.microsoft.com/pricing/details/site-recovery/) és [itt.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Vegye figyelembe, hogy a zónák és zónák vészhelyreállítása esetén a bejövő forgalom díja alacsonyabb, mint a régiók és régiók vészhelyreállítása.

**2. Mi az RTO és RPO SLA-ja?**
Az RTO SLA megegyezik a teljes Site Recovery sla-val. Az RTO legfeljebb 2 óra lehet. Az RPO nem határoz meg SLA-t.

**3. A kapacitás garantált a másodlagos zónában?**
Az Site Recovery és az Azure kapacitáskezelési csapata megfelelő infrastruktúra-kapacitást tervez. Amikor feladatátvételt indít el, a csapatok azt is biztosítják, hogy a virtuális gép által Site Recovery virtuálisgép-példányok üzembe helyezése a célzónában történik.

**4. Mely operációs rendszerek támogatottak?**
A zónák és zónák vészhelyreállítása ugyanazt az operációs rendszert támogatja, mint az Azure-beli vészhelyreállítás. A támogatási mátrixot itt [tekintse meg.](./azure-to-azure-support-matrix.md)

**5. A forrás- és célerőforráscsoportok azonosak?**
Nem, a feladatátvételt másik erőforráscsoportba kell átvesznie.

## <a name="next-steps"></a>Következő lépések

A vészhelyreállítási próba, feladatátvétel, védelem helyreállítása és feladat-visszavétel futtatásához szükséges lépések ugyanazok, mint az Azure–Azure vészhelyreállítási forgatókönyv lépései.

Vészhelyreállítási próba végrehajtásához kövesse az itt ismertetett [lépéseket.](./azure-to-azure-tutorial-dr-drill.md)

A másodlagos zónában található virtuális gépek feladatátvételének és vélmének ismételt végrehajtásához kövesse az itt leírt [lépéseket.](./azure-to-azure-tutorial-failover-failback.md)

Az elsődleges zónába való feladat-visszavételhez kövesse az itt ismertetett [lépéseket.](./azure-to-azure-tutorial-failback.md)
