---
title: Az Azure-Virtual Machines zóna vész-helyreállításának engedélyezése a zónában
description: Ez a cikk azt ismerteti, hogy mikor és hogyan használható a zóna az Azure-beli virtuális gépekkel kapcsolatos vész-helyreállításhoz.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: d207a5629647ef3582f1e0490ef6ccc407f935f7
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584838"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Azure-beli virtuális gép vész-helyreállításának engedélyezése rendelkezésre állási zónák között

Ez a cikk az Azure-beli virtuális gépek egyik rendelkezésre állási zónából egy másikba való replikálását, feladatátvételét és feladat-visszavételét ismerteti ugyanazon az Azure-régióban.

>[!NOTE]
>
>- A zóna és a zóna közötti vész-helyreállítás támogatása jelenleg kilenc régióra korlátozódik: Délkelet-Ázsia, Kelet-Japán, Kelet-Ausztrália, Egyesült Királyság déli régiója, Nyugat-Európa, Észak-Európa, az USA középső régiója, az USA keleti régiója és az USA nyugati régiója 2.  
>- A Site Recovery nem helyezi át és nem tárolja az ügyféladatokat azon a régión kívül, amelyben az ügyfél üzembe helyezése történik, amikor a felhasználó a zónát használja a zóna vész-helyreállítási feladataihoz. Az ügyfelek választhatnak egy Recovery Services-tárolót egy másik régióból is. A Recovery Services-tároló metaadatokat tartalmaz, de nincsenek tényleges ügyféladatok.

A Site Recovery szolgáltatás a tervezett és nem tervezett leállások során az üzletmenet-folytonosságot és a vész-helyreállítási stratégiát segíti az üzleti alkalmazások működésének megtartásában. Az ajánlott vész-helyreállítási lehetőség, hogy az alkalmazásait akár regionális kimaradás esetén is meg kell őrizni.

A rendelkezésreállási zónák fizikailag elkülönített helyek egy Azure-régión belül. Minden zónához egy vagy több adatközpont tartozik. 

Ha a virtuális gépeket egy másik régióban lévő rendelkezésre állási zónába szeretné áthelyezni, [tekintse át ezt a cikket](../resource-mover/move-region-availability-zone.md).

## <a name="using-availability-zones-for-disaster-recovery"></a>A Availability Zones használata a vész-helyreállításhoz 

Availability Zones a virtuális gépek magas rendelkezésre állású konfigurációban való üzembe helyezésére szolgálnak. Előfordulhat, hogy túl közel vannak egymáshoz, hogy vészhelyzeti helyreállítási megoldásként szolgálnak a természeti katasztrófák esetén.

Bizonyos helyzetekben azonban a Availability Zones a vész-helyreállításhoz is kihasználható:

- Sok ügyfelünk, akik egy Metro vész-helyreállítási stratégiával rendelkeztek, és az alkalmazások helyszíni üzemeltetése során időnként úgy tekintenek, hogy ezt a stratégiát úgy utánozzák, hogy az alkalmazásokat az Azure-ba Ezek az ügyfelek tudomásul veszik, hogy a Metro vész-helyreállítási stratégia nem működik nagy mennyiségű fizikai katasztrófa esetén, és elfogadja ezt a kockázatot. Az ilyen ügyfelek esetében a zóna és a zóna vész-helyreállítási lehetősége vész-helyreállítási megoldásként használható.

- Számos más ügyfél bonyolult hálózati infrastruktúrával rendelkezik, és nem kívánja újra létrehozni azt egy másodlagos régióban a kapcsolódó költségeket és összetettséget figyelembe véve. A Zone to Zone vész-helyreállítási szolgáltatás csökkenti a bonyolultságot, mivel a redundáns hálózatkezelési fogalmakat használja Availability Zones a konfigurálás sokkal egyszerűbbé tétele érdekében. Az ilyen ügyfelek előnyben részesítik az egyszerűséget, és a Availability Zones is használhatják a vész-helyreállításhoz.

- Egyes régiókban, amelyekben nem szerepel a párosított régió ugyanazon a jogi joghatóság alatt (például Délkelet-Ázsiában), a zóna-zóna vész-helyreállítási megoldásként a katasztrófa-visszaeséses helyreállítási megoldásként szolgálhat, mivel az alkalmazások és az információk nem kerülnek át a nemzeti határokon. 

- A zónák közötti vész-helyreállítás az Azure-hoz az Azure vész-helyreállításhoz képest rövidebb idő alatt replikálja az adatreplikációt, így az alacsonyabb késés és az alacsonyabb RPO is megjelenhet.

Habár ezek jelentős előnyökkel járnak, lehetséges, hogy a zónák közötti vész-helyreállítás a teljes régióra kiterjedő természeti katasztrófák esetén a rugalmasságra vonatkozó követelmények hiányában is csökkenhet.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>A zóna és a zóna vész-helyreállításának hálózatkezelése

A fentiekben leírtaknak megfelelően a Zone to Zone vész-helyreállítási szolgáltatás csökkenti a bonyolultságot, mivel a redundáns hálózatkezelési fogalmakat használja Availability Zones a konfiguráció sokkal egyszerűbbé tétele érdekében. A zónában lévő hálózatkezelési összetevők viselkedése a zóna vész-helyreállítási forgatókönyve a következő: 

- Virtual Network: ugyanazt a virtuális hálózatot használja, mint a forrásoldali hálózat a tényleges feladatátvételekhez. Használjon másik virtuális hálózatot a forrásként szolgáló virtuális hálózathoz a feladatátvételi tesztekhez.

- Alhálózat: az azonos alhálózatra történő feladatátvétel támogatott.

- Magánhálózati IP-cím: Ha statikus IP-címeket használ, akkor ugyanazokat az IP-címeket használhatja a célként megadott zónában, ha úgy dönt, hogy azokat ilyen módon konfigurálja.

- Gyorsított hálózatkezelés: az Azure-hoz az Azure vész-helyreállításhoz hasonlóan a gyorsított hálózatkezelést is engedélyezheti, ha a VM SKU támogatja.

- Nyilvános IP-cím: egy korábban létrehozott szabványos nyilvános IP-címet is csatolhat ugyanabban a régióban a célként megadott virtuális géphez. Az alapszintű nyilvános IP-címek nem támogatják a rendelkezésre állási zónákhoz kapcsolódó forgatókönyveket.

- Load Balancer: a standard Load Balancer egy regionális erőforrás, ezért a célként megadott virtuális gép csatlakoztatható ugyanahhoz a terheléselosztó háttér-készletéhez. Nincs szükség új Load balancerre.

- Hálózati biztonsági csoport: ugyanazokat a hálózati biztonsági csoportokat használhatja, mint amelyek a forrásoldali virtuális gépen is alkalmazhatók.

## <a name="pre-requisites"></a>Előfeltételek

Mielőtt üzembe helyezi a zónát a virtuális gépek számára a zóna vész-helyreállításához, fontos, hogy a virtuális gépen elérhető egyéb szolgáltatások a zónákhoz is használhatók legyenek.

|Szolgáltatás  | Támogatási nyilatkozat  |
|---------|---------|
|A klasszikus virtuális gépeket   |     Nem támogatott    |
|ARM virtuális gépek    |    Támogatott    |
|Azure Disk Encryption v1 (Dual Pass, Azure Active Directory (Azure AD))     |     Támogatott   |
|Azure Disk Encryption v2 (Single pass, Azure AD nélkül)    |    Támogatott    |
|Nem felügyelt lemezek    |    Nem támogatott    |
|Felügyelt lemezek    |    Támogatott    |
|Felhasználó által kezelt kulcsok    |    Támogatott    |
|Közelségi elhelyezési csoportok    |    Támogatott    |
|Biztonsági mentési együttműködés    |    A fájlok szintjének biztonsági mentése és visszaállítása támogatott. A lemezes és a virtuális gép szintjének biztonsági mentése és visszaállítása nem támogatott.    |
|Gyors Hozzáadás/Eltávolítás    |    Lemezek a zónák replikálásának engedélyezése után adhatók hozzá. A lemezek eltávolítása a zónák replikálásának engedélyezése után nem támogatott.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Site Recovery zónák beállítása a zóna vész-helyreállításához

### <a name="log-in"></a>Bejelentkezés

Jelentkezzen be az Azure Portalra.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Replikáció engedélyezése a Zona Azure-beli virtuális gép számára

1. A Azure Portal menüben válassza a virtuális gépek lehetőséget, vagy keresse meg és válassza ki a virtuális gépeket bármely oldalon. Válassza ki a replikálni kívánt virtuális gépet. Ahhoz, hogy a zóna vész-helyreállítási zónába kerüljön, a virtuális gépnek már rendelkezésre állási zónában kell lennie.

2. A Műveletek részen válassza a Vészhelyreállítás elemet.

3. Ahogy az alábbi ábrán is látható, az alapok lapon válassza az Igen lehetőséget a "vész-helyreállítás Availability Zones között?" lehetőségre.

    ![Alapszintű beállítások lap](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Ha elfogadja az összes alapértelmezett beállítást, kattintson a "felülvizsgálat + replikáció indítása" elemre, majd a "replikáció indítása" gombra.

5. Ha módosítani szeretné a replikációs beállításokat, kattintson a Tovább: speciális beállítások elemre.

6. Ha szükséges, módosítsa a beállításokat az alapértelmezetttől függetlenül. Az Azure-ból az Azure-ba irányuló vész-helyreállítási felhasználók számára ez az oldal ismerős lehet. Az ezen a panelen bemutatott beállításokkal kapcsolatos további részletek [itt](./azure-to-azure-tutorial-enable-replication.md) találhatók

    ![Speciális beállítások lap](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Kattintson a következőre: felülvizsgálat + replikáció indítása, majd a "replikáció elindítása".

## <a name="faqs"></a>Gyakori kérdések

**1. Hogyan működik az árképzés a zónák közötti vész-helyreállításhoz?**
A zónák közötti vész-helyreállítás díjszabása megegyezik az Azure és az Azure vész-helyreállítás díjszabásával. További részleteket a díjszabási [oldalról itt talál](https://azure.microsoft.com/pricing/details/site-recovery/) [.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Vegye figyelembe, hogy az olyan kimenő forgalom díja, amelyet a zónában, a zóna vész-helyreállítási feladatoknál látni fog, alacsonyabb lesz, mint a régió-és a katasztrófa utáni

**2. Mi a RTO és a RPO SLA-ja?**
A RTO SLA ugyanaz, mint a Site Recovery általános. A RTO akár 2 óráig is Megígérjük. Nincs definiált SLA a RPO számára.

**3. a kapacitás garantált a másodlagos zónában?**
A Site Recovery csapat és az Azure Capacity Management csapata elegendő infrastrukturális kapacitást tervez. A feladatátvétel indításakor a csapatok is segítenek biztosítani, hogy Site Recovery által védett virtuálisgép-példányok a célzóna számára legyenek telepítve.

**4. mely operációs rendszerek támogatottak?**
A zónák közötti vész-helyreállítási szolgáltatás ugyanazokat az operációs rendszereket támogatja, mint az Azure-ban az Azure vész-helyreállítás. Tekintse meg a támogatási mátrixot [itt](./azure-to-azure-support-matrix.md).

**5. a forrás-és a célként megadott erőforráscsoportok is megegyeznek?**
Nem, a feladatátvételt egy másik erőforráscsoporthoz kell átadnia.

## <a name="next-steps"></a>Következő lépések

A vész-helyreállítási részletezés, feladatátvétel, ismételt védelem és feladat-visszavétel futtatásához követendő lépések megegyeznek az Azure-beli vész-helyreállítási forgatókönyv lépéseivel.

A vész-helyreállítási részletezés végrehajtásához kövesse az [itt](./azure-to-azure-tutorial-dr-drill.md)ismertetett lépéseket.

A másodlagos zónában lévő virtuális gépek feladatátvételének és újravédésének végrehajtásához kövesse az [itt](./azure-to-azure-tutorial-failover-failback.md)ismertetett lépéseket.

Az elsődleges zónába történő feladat-visszavételhez kövesse az [itt](./azure-to-azure-tutorial-failback.md)ismertetett lépéseket.
