---
title: Tudnivalók a helyi alkalmazások vész-helyreállításáról Azure Site Recovery
description: Azokat a számítási feladatokat ismerteti, amelyeket meg lehet védeni a vészhelyreállítás Azure Site Recovery szolgáltatással történő használatával.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80062844"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Információk a helyszíni alkalmazások vészhelyreállításáról

Ez a cikk azokat a helyszíni munkaterheléseket és alkalmazásokat ismerteti, amelyeket a [Azure site Recovery](site-recovery-overview.md) szolgáltatással szembeni vész-helyreállítási védelemmel lehet ellátni.

## <a name="overview"></a>Áttekintés

A szervezeteknek egy üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiára van szükségük a számítási feladatok és adatok biztonságos és a tervezett és nem tervezett leállások során elérhetővé tételéhez. És állítsa helyre a szokásos munkakörülményeket.

Az Azure Site Recovery segít a BCDR-stratégia kidolgozásában. A Site Recovery használatával alkalmazásbarát replikációt végezhet a felhőbe vagy egy másodlagos helyre. A Site Recovery segítségével kezelheti a replikációt, elvégezheti a vész-helyreállítási tesztelést, valamint feladatátvételeket és feladat-visszavételt futtathat. Alkalmazásai Windows vagy Linux rendszerű számítógépeken, fizikai kiszolgálókon, VMware-en vagy Hyper-V-n is futtathatók.

A Site Recovery a Microsoft-alkalmazásokkal, például a SharePoint, az Exchange, a Dynamics, a SQL Server és a Active Directory szolgáltatással integrálható. A Microsoft szorosan együttműködik az olyan vezető szállítókkal, mint az Oracle, az SAP vagy a Red Hat. A replikációs megoldásokat alkalmazásonként szabhatja testre.

## <a name="why-use-site-recovery-for-application-replication"></a>Miért előnyös a Site Recovery használata az alkalmazásreplikációhoz?

A Site Recovery az alábbi módokon járul hozzá az alkalmazásszintű védelemhez és helyreállításhoz:

- Az App-agnosztikus és replikációt biztosít a támogatott gépen futó összes munkaterheléshez.
- Közel szinkron replikáció, a helyreállítási pontok célkitűzéseivel (RPO), a legkritikusabb üzleti alkalmazások igényeinek kielégítése érdekében.
- Alkalmazáskonzisztens pillanatképeket rögzít egy- vagy többszintű alkalmazásokról.
- Integráció SQL Server AlwaysOn, és együttműködés más alkalmazás-szintű replikációs technológiákkal. Például Active Directory replikáció, SQL AlwaysOn és Exchange Database rendelkezésre állási csoportok (Dag).
- Rugalmas helyreállítási tervek, amelyek lehetővé teszik egy teljes alkalmazás-verem helyreállítását egyetlen kattintással, valamint külső parancsfájlokat és manuális műveleteket is tartalmaz a tervben.
- A Site Recovery és az Azure speciális hálózatkezelési szolgáltatásával egyszerűsítheti az alkalmazások hálózati követelményeit. A hálózatkezelést, például az IP-címek fenntartásának lehetőségét, a terheléselosztás konfigurálását és az Azure Traffic Manager való integrációt az alacsony helyreállítási idejű célkitűzések (RTO) hálózati hálózatváltást.
- A szolgáltatás gazdag, éles használatra kész és alkalmazásspecifikus parancsprogramokat tartalmazó automatizációs kódtárat tartalmaz, amely letölthető, és beépíthető a helyreállítási tervekbe.

## <a name="workload-summary"></a>A számítási feladatok összefoglalása

A Site Recovery a támogatott gépeken futó bármilyen alkalmazást képes replikálni. A következő táblázatban megadott alkalmazások további teszteléséhez partnereinkkel foglalkozunk.

| **Számítási feladat** |**Virtuális gépek replikálása az Azure-ba** |**Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Igen |Igen |Igen |Igen |Yes|
| Webalkalmazások (IIS, SQL) |Igen |Igen |Igen |Igen |Yes|
| System Center Operations Manager |Igen |Igen |Igen |Igen |Yes|
| SharePoint |Igen |Igen |Igen |Igen |Yes|
| SAP<br/><br/>Replikálás SAP-helyről az Azure-ba nem fürtözött rendszer esetén |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által)|
| Exchange (nem DAG) |Igen |Igen |Igen |Igen |Yes|
| Távoli asztal/VDI |Igen |Igen |Igen |Igen |Yes|
| Linux (operációs rendszer és alkalmazások) |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által) |Igen (tesztelte a Microsoft által)|
| Dynamics AX |Igen |Igen |Igen |Igen |Yes|
| Windows fájlkiszolgáló |Igen |Igen |Igen |Igen |Yes|
| Citrix XenApp és XenDesktop |Yes|N.A. |Igen |N.A. |Igen |

## <a name="replicate-active-directory-and-dns"></a>Active Directory és DNS replikálása

A legtöbb vállalati alkalmazás számára elengedhetetlen az Active Directory- és DNS-infrastruktúra használata. A vész-helyreállítás során a számítási feladatok és alkalmazások helyreállítása előtt el kell végeznie az infrastruktúra-összetevők megóvását és helyreállítását.

A Site Recovery segítséglével teljesen automatizált vészhelyreállítási tervet hozhat létre az Active Directoryhoz és a DNS-hez. Ha például egy elsődleges helyről egy másodlagos helyre szeretné felvenni a SharePoint és az SAP protokollt, beállíthat egy helyreállítási tervet, amely az első feladatátvételt Active Directory. Ezután használjon egy további alkalmazásspecifikus helyreállítási tervet a Active Directory-ra támaszkodó egyéb alkalmazások feladatátvételéhez.

[További](site-recovery-active-directory.md) információ a Active Directory és a DNS vész-helyreállításáról.

## <a name="protect-sql-server"></a>Az SQL Server védelme

A SQL Server a helyszíni adatközpontokban számos üzleti alkalmazás adatszolgáltatási alapjait biztosítja. A Site Recovery SQL Server HA/DR technológiákkal is használható a SQL Servert használó többrétegű vállalati alkalmazások számára.

A Site Recovery a következőket biztosítja:

- Egyszerű és költséghatékony vészhelyreállítási megoldás az SQL Serverhez. Az SQL Server önálló kiszolgálói és fürtjei több verziójának és kiadásának replikálása az Azure-ba vagy egy másodlagos helyre.
- Integrálható SQL AlwaysOn rendelkezésre állási csoportokkal, az Azure Site Recovery helyreállítási tervek feladatátvételének és feladat-visszavételének kezeléséhez.
- Végpontok közötti helyreállítási tervek az alkalmazás összes rétege számára, ideértve az SQL Server-adatbázisokat is.
- A maximális terhelések SQL Server méretezése Site Recoveryekkel _, az Azure_ -ban nagyobb IaaS virtuális gépek méretével.
- Az SQL Server vészhelyreállítási funkcióinak egyszerű tesztelése. Feladatátvételi tesztek segítségével elemezheti az adatokat és megfelelőségi ellenőrzést futtathat az éles környezet befolyásolása nélkül.

[További](site-recovery-sql.md) információ az SQL Server vész-helyreállításáról.

## <a name="protect-sharepoint"></a>A SharePoint védelme

Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a SharePoint-környezetek védelmét:

- Használata esetén nem szükséges költséges készenléti kiszolgálófarmot kiépítenie a vészhelyreállításhoz. A Site Recovery használatával teljes farmot (webes, alkalmazás-és adatbázis-szinteket) replikálhat az Azure-ba vagy egy másodlagos helyre.
- Leegyszerűsíti az alkalmazások üzembe helyezését és felügyeletét. Az elsődleges helyen telepített frissítések automatikusan replikálódnak. A frissítések egy másodlagos helyen lévő Farm feladatátvétele és helyreállítása után érhetők el. Csökkenti a felügyelet összetettségét és a készenléti Farm naprakészen tartásával járó költségeket.
- Leegyszerűsíti a SharePoint-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti replikakörnyezet hozható létre a teszteléshez és a hibakereséshez.
- Megkönnyíti a felhőre való átállást, mivel a SharePoint üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.

[További](site-recovery-sharepoint.md) információ a SharePoint rendszerhez készült vész-helyreállításról.

## <a name="protect-dynamics-ax"></a>A Dynamics AX védelme

Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a Dynamics AX ERP-megoldás védelmét:

- A teljes Dynamics AX-környezet (webes és AOS rétegek, adatbázis-rétegek, SharePoint) replikációjának kezelése az Azure-ba vagy egy másodlagos helyre.
- Leegyszerűsíti a Dynamics AX üzemelő példányok felhőbe (Azure-ba) való áttelepítését.
- Leegyszerűsíti a Dynamics AX-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti másolat hozható létre a teszteléshez és a hibakereséshez.

[További](site-recovery-dynamicsax.md) információ a dinamikus AX vész-helyreállításáról.

## <a name="protect-remote-desktop-services"></a>Védelem Távoli asztali szolgáltatások

A Távoli asztali szolgáltatások (RDS) lehetővé teszi a virtuális asztali infrastruktúra (VDI), a munkamenet-alapú asztalok és alkalmazások használatát, amelyekkel a felhasználók bárhol dolgozhatnak.

A Azure Site Recovery a következő szolgáltatásokat lehet replikálni:

- Felügyelt vagy nem kezelt, készletezett virtuális asztalok replikálása másodlagos helyre.
- Távoli alkalmazások és munkamenetek replikálása egy másodlagos helyre vagy az Azure-ba.

A következő táblázat a replikációs beállításokat tartalmazza:

| **Távoli asztali szolgáltatások** |**Virtuális gépek replikálása az Azure-ba** | **Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** | **Fizikai kiszolgálók replikálása egy másodlagos helyre** | **Fizikai kiszolgálók replikálása az Azure-ba** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Készletbe vont virtuális asztalok (nem felügyelt)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Készletbe vont virtuális asztalok (felügyelt, UPD nélkül)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Távoli alkalmazások és asztali munkamenetek (UPD nélkül)** |Igen|Igen |Igen |Igen |Igen |Igen |Yes |

[További](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) információ az RDS-sel kapcsolatos vész-helyreállításról.

## <a name="protect-exchange"></a>Az Exchange védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az Exchange-környezetek védelmét:

- A kisméretű Exchange-környezetek, például az önálló kiszolgálók esetén a Site Recovery képes az Azure-ba vagy egy másodlagos helyre történő replikálásra és feladatátvitelre.
- Nagyobb méretű környezetek esetén a Site Recovery integrálható az Exchange DAG-kkal.
- Vállalati környezetben az Exchange-vészhelyreállításhoz az Exchange DAG-k használatát javasoljuk.  A Site Recovery helyreállítási tervekbe a DAG-k is bevehetők a helyek közötti DAG feladatátvitelhez.

További információ az Exchange-alapú vész-helyreállításról: [Exchange Dag](/Exchange/high-availability/database-availability-groups/database-availability-groups) és Exchange vész- [helyreállítás](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Az SAP védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az SAP védelmét:

- Lehetővé teszi a helyszínen futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők az Azure-ba való replikálásával.
- Lehetővé teszi az Azure-t futtató SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők egy másik Azure-adatközpontba való replikálásával.
- Megkönnyíti a felhőre való áttelepítést, mivel az SAP üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
- Leegyszerűsíti az SAP projektek frissítését, tesztelését és prototípuskészítését, mivel a segítségével egy igény szerinti éles klón hozható létre az SAP alkalmazások teszteléséhez.

[További](site-recovery-sap.md) információ az SAP-beli vész-helyreállításról.

## <a name="protect-internet-information-services"></a>Védelem Internet Information Services

A Internet Information Services (IIS) üzemelő példányának a Site Recovery használatával történő ellátásához használja az alábbiakat:

Az Azure Site Recovery úgy gondoskodik a vészhelyreállításról, hogy a környezet kritikus összetevőit replikálja egy offline távoli helyre, vagy egy, a Microsoft Azure-hoz hasonló nyilvános felhőbe. Mivel a webkiszolgálóval és az adatbázissal rendelkező virtuális gépek replikálódnak a helyreállítási helyre, nincs szükség a konfigurációs fájlok vagy tanúsítványok külön biztonsági mentésére. A feladatátvétel után módosult környezeti változóktól függő alkalmazásleképezések és -kötések a vészhelyreállítás tervekbe integrált szkriptekkel frissíthetők. A virtuális gépek csak feladatátvétel közben kerülnek a helyreállítási helyre. A Azure Site Recovery a teljes körű feladatátvételt is segíti a következő lehetőségek megadásával:

- A virtuális gépek leállításának és indításának sorrendbe állítása a különböző szinteken.
- Parancsfájlok hozzáadásával engedélyezheti az alkalmazás-függőségek és-kötések frissítését a virtuális gépeken az elindítása után. A szkriptek a DNS-kiszolgáló frissítésére is használhatók, hogy a helyreállítási helyre mutasson.
- IP-címek lefoglalása a virtuális gépek előtti feladatátvételhez az elsődleges és a helyreállítási hálózatok leképezésével, valamint olyan parancsfájlok használata, amelyeket nem szükséges frissíteni a feladatátvétel után.
- Egykattintásos feladatátvétel lehetősége több webalkalmazás esetében, amely kiküszöböli a vészhelyzet során felmerülő zavart.
- Lehetőség a helyreállítási tervek tesztelésére egy DR-részletezések számára elkülönített környezetben.

[További](site-recovery-iis.md) információ az IIS vész-helyreállításáról.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>A Citrix XenApp és a XenDesktop védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az üzemelő Citrix XenApp- és XenDesktop-példány védelmét:

- Engedélyezze a Citrix XenApp és a XenDesktop telepítés védelmét. Replikálja a különböző telepítési rétegeket az Azure-ba: Active Directory, DNS-kiszolgáló, SQL Database-kiszolgáló, Citrix Delivery Controller, kirakat-kiszolgáló, XenApp Master (VDA), Citrix XenApp License Server.
- Megkönnyíti a felhőre való áttelepítést, mivel az üzemelő Citrix XenApp- és XenDesktop-példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
- Leegyszerűsíti a Citrix XenApp-/XenDesktop-fejlesztést és -tesztelést, mivel segítségével az éleshez hasonló, igény szerinti másolat hozható létre az alkalmazások teszteléséhez és a hibakereséséhez.
- Ez a megoldás csak a Windows Server rendszerű virtuális asztalokra vonatkozik, nem pedig az ügyfél virtuális asztalára. Az ügyfél virtuális asztalok még nem támogatottak az Azure-beli licenceléshez. [Itt részletesen tájékozódhat](https://azure.microsoft.com/pricing/licensing-faq/) az ügyfél/kiszolgáló asztali környezeteinek Azure-ban történő licenceléséről.

[További](site-recovery-citrix-xenapp-and-xendesktop.md) információ a Citrix XenApp és a XenDesktop üzemelő példányok vész-helyreállításáról. Vagy tekintse át a [Citrix](https://aka.ms/citrix-xenapp-xendesktop-with-asr)tanulmányt.

## <a name="next-steps"></a>Következő lépések

[További](azure-to-azure-quickstart.md) információ az Azure-beli virtuális gépek vész-helyreállításáról.
