---
title: Helyszíni alkalmazások vészhelyreállításának Azure Site Recovery
description: Azokat a számítási feladatokat ismerteti, amelyeket meg lehet védeni a vészhelyreállítás Azure Site Recovery szolgáltatással történő használatával.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1a5d20e6feacfe72052142c07dc45753b9bc3138
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599111"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Információk a helyszíni alkalmazások vészhelyreállításáról

Ez a cikk azokat a helyszíni számítási feladatokat és alkalmazásokat ismerteti, amelyek vészhelyreállítási védelmet kínálnak a [Azure Site Recovery](site-recovery-overview.md) szolgáltatással.

## <a name="overview"></a>Áttekintés

A szervezeteknek üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára van szükségük a számítási feladatok és az adatok biztonságának és rendelkezésre állásának biztosításához a tervezett és nem tervezett leállások során. A helyreállítás pedig normál feltételeinek megfelelően is meg fog állni.

Az Azure Site Recovery segít a BCDR-stratégia kidolgozásában. A Site Recovery használatával alkalmazásbarát replikációt végezhet a felhőbe vagy egy másodlagos helyre. A biztonsági Site Recovery a replikáció kezelésére, vészhelyreállítási tesztek elvégzésére, valamint feladatátvételek és feladat-visszavételek futtatására. Az alkalmazások Windows- vagy Linux-alapú számítógépeken, fizikai kiszolgálókon, VMware-ben vagy Hyper-V-ben futtathatók.

Site Recovery integrálható olyan Microsoft-alkalmazásokkal, mint a SharePoint, az Exchange, a Dynamics, SQL Server és Active Directory. A Microsoft szorosan együttműködik olyan vezető szállítóval, mint az Oracle, az SAP és a Red Hat. A replikációs megoldásokat alkalmazásonként szabhatja testre.

## <a name="why-use-site-recovery-for-application-replication"></a>Miért előnyös a Site Recovery használata az alkalmazásreplikációhoz?

A Site Recovery az alábbi módokon járul hozzá az alkalmazásszintű védelemhez és helyreállításhoz:

- Alkalmazástól független, és replikációt biztosít a támogatott gépeken futó számítási feladatokhoz.
- Közel szinkron replikáció, akár 30 másodperces helyreállításipont-célkitűzésekkel (RPO) a legkritikusabb üzleti alkalmazások igényeinek megfelelően.
- Alkalmazáskonzisztens pillanatképeket rögzít egy- vagy többszintű alkalmazásokról.
- Integráció az SQL Server AlwaysOn-nal, és együttműködés más alkalmazásszintű replikációs technológiákkal. Ilyen például a replikációs Active Directory, az SQL AlwaysOn és az Exchange Database rendelkezésre állási csoportok (DAG-k).
- Rugalmas helyreállítási tervek, amelyek lehetővé teszik egy teljes alkalmazáskészlet egyetlen kattintással történő helyreállítását, valamint külső szkriptek és manuális műveletek tervbe való mentését.
- Fejlett hálózatkezelés a Site Recovery azure-ban az alkalmazáshálózati követelmények egyszerűsítése érdekében. Hálózatkezelés, például IP-címek lefoglalásának lehetősége, terheléselosztás konfigurálása és integráció Azure Traffic Manager alacsony helyreállítási időre vonatkozó célkitűzések (RTO) hálózati váltása érdekében.
- A szolgáltatás gazdag, éles használatra kész és alkalmazásspecifikus parancsprogramokat tartalmazó automatizációs kódtárat tartalmaz, amely letölthető, és beépíthető a helyreállítási tervekbe.

## <a name="workload-summary"></a>A számítási feladatok összefoglalása

A Site Recovery a támogatott gépeken futó bármilyen alkalmazást képes replikálni. Termékcsapatokkal is partneri kapcsolatban vagyunk, hogy további teszteket is el tudjanak látni az alábbi táblázatban megadott alkalmazásokon.

| **Számítási feladat** |**Virtuális gépek replikálása az Azure-ba** |**Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Igen |Igen |Igen |Igen |Yes|
| Webalkalmazások (IIS, SQL) |Igen |Igen |Igen |Igen |Yes|
| System Center Operations Manager |Igen |Igen |Igen |Igen |Yes|
| SharePoint |Igen |Igen |Igen |Igen |Yes|
| SAP<br/><br/>Replikálás SAP-helyről az Azure-ba nem fürtözött rendszer esetén |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte)|
| Exchange (nem DAG) |Igen |Igen |Igen |Igen |Yes|
| Távoli asztal/VDI |Igen |Igen |Igen |Igen |Yes|
| Linux (operációs rendszer és alkalmazások) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte)|
| Dynamics AX |Igen |Igen |Igen |Igen |Yes|
| Windows fájlkiszolgáló |Igen |Igen |Igen |Igen |Yes|
| Citrix XenApp és XenDesktop |No|N/A |No |N/A |No |

## <a name="replicate-active-directory-and-dns"></a>Active Directory és DNS replikálása

A legtöbb vállalati alkalmazás számára elengedhetetlen az Active Directory- és DNS-infrastruktúra használata. A vészhelyreállítás során ezeknek az infrastruktúra-összetevőknek a védelmét és helyreállítását még a számítási feladatok és alkalmazások helyreállítása előtt kell helyreállítani.

A Site Recovery segítséglével teljesen automatizált vészhelyreállítási tervet hozhat létre az Active Directoryhoz és a DNS-hez. Ha például a SharePoint és az SAP feladatátvételét egy elsődleges helyről egy másodlagosra tervezi, létrehozhat egy helyreállítási tervet, amely először feladatátvételt Active Directory. Ezután egy további alkalmazásspecifikus helyreállítási terv használatával feladatátvételt is használhat az alkalmazásra támaszkodó többi Active Directory.

[További információ a](site-recovery-active-directory.md) vészhelyreállításról Active Directory DNS-hez.

## <a name="protect-sql-server"></a>Az SQL Server védelme

SQL Server a helyszíni adatközpontban található számos üzleti alkalmazáshoz biztosít adatszolgáltatás-alapot. Site Recovery a SQL Server/DR technológiákkal együtt használhatók a többrétegű vállalati alkalmazások védelmére, amelyek SQL Server.

A Site Recovery a következőket biztosítja:

- Egyszerű és költséghatékony vészhelyreállítási megoldás az SQL Serverhez. Az SQL Server önálló kiszolgálói és fürtjei több verziójának és kiadásának replikálása az Azure-ba vagy egy másodlagos helyre.
- Integrálható SQL AlwaysOn rendelkezésre állási csoportokkal, az Azure Site Recovery helyreállítási tervek feladatátvételének és feladat-visszavételének kezeléséhez.
- Végpontok közötti helyreállítási tervek az alkalmazás összes rétege számára, ideértve az SQL Server-adatbázisokat is.
- A csúcsterhelések SQL Server méretezése a Site Recovery, ha  nagyobb IaaS virtuálisgép-méretekre eszkalálják őket az Azure-ban.
- Az SQL Server vészhelyreállítási funkcióinak egyszerű tesztelése. Feladatátvételi tesztek segítségével elemezheti az adatokat és megfelelőségi ellenőrzést futtathat az éles környezet befolyásolása nélkül.

[További információ az](site-recovery-sql.md) SQL Server vészhelyreállítási szolgáltatásról.

## <a name="protect-sharepoint"></a>A SharePoint védelme

Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a SharePoint-környezetek védelmét:

- Használata esetén nem szükséges költséges készenléti kiszolgálófarmot kiépítenie a vészhelyreállításhoz. A Site Recovery farm (webes, alkalmazás- és adatbázisszintek) replikálása az Azure-ba vagy egy másodlagos webhelyre.
- Leegyszerűsíti az alkalmazások üzembe helyezését és felügyeletét. Az elsődleges helyen telepített frissítéseket a rendszer automatikusan replikálja. A frissítések egy másodlagos helyen található farm feladatátvétele és helyreállítása után érhetők el. Csökkenti a felügyelet összetettségét és a készenléti farm naprakészentartásával kapcsolatos költségeket.
- Leegyszerűsíti a SharePoint-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti replikakörnyezet hozható létre a teszteléshez és a hibakereséshez.
- Megkönnyíti a felhőre való átállást, mivel a SharePoint üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.

[További információ a](site-recovery-sharepoint.md) SharePoint vészhelyreállítási szolgáltatásról.

## <a name="protect-dynamics-ax"></a>A Dynamics AX védelme

Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a Dynamics AX ERP-megoldás védelmét:

- A teljes Dynamics AX-környezet replikációjának kezelése (webes és AOS-szintek, adatbázisszintek, SharePoint) az Azure-ba vagy egy másodlagos webhelyre.
- Leegyszerűsíti a Dynamics AX üzemelő példányok felhőbe (Azure-ba) való áttelepítését.
- Leegyszerűsíti a Dynamics AX-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti másolat hozható létre a teszteléshez és a hibakereséshez.

[További információ a](site-recovery-dynamicsax.md) Dynamic AX vészhelyreállításával kapcsolatban.

## <a name="protect-remote-desktop-services"></a>A Távoli asztali szolgáltatások

Távoli asztali szolgáltatások (RDS) lehetővé teszi a virtuális asztali infrastruktúrát (VDI), munkamenet-alapú asztalokat és alkalmazásokat, amelyek lehetővé teszik a felhasználók számára, hogy bárhol dolgoznak.

A Azure Site Recovery a következő szolgáltatásokat replikálhatja:

- Felügyelt vagy nem felügyelt, készletbekészletett virtuális asztalokat replikál egy másodlagos helyre.
- Távoli alkalmazások és munkamenetek replikálása másodlagos helyekre vagy az Azure-ba.

Az alábbi táblázat a replikációs lehetőségeket mutatja be:

| **Távoli asztali szolgáltatások** |**Virtuális gépek replikálása az Azure-ba** | **Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** | **Fizikai kiszolgálók replikálása egy másodlagos helyre** | **Fizikai kiszolgálók replikálása az Azure-ba** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Készletbe vont virtuális asztalok (nem felügyelt)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Készletbe vont virtuális asztalok (felügyelt, UPD nélkül)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Távoli alkalmazások és asztali munkamenetek (UPD nélkül)** |Igen|Igen |Igen |Igen |Igen |Igen |Yes |

[További információ az](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) RDS-vészhelyreállításról.

## <a name="protect-exchange"></a>Az Exchange védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az Exchange-környezetek védelmét:

- A kisméretű Exchange-környezetek, például az önálló kiszolgálók esetén a Site Recovery képes az Azure-ba vagy egy másodlagos helyre történő replikálásra és feladatátvitelre.
- Nagyobb méretű környezetek esetén a Site Recovery integrálható az Exchange DAG-kkal.
- Vállalati környezetben az Exchange-vészhelyreállításhoz az Exchange DAG-k használatát javasoljuk.  A Site Recovery helyreállítási tervekbe a DAG-k is bevehetők a helyek közötti DAG feladatátvitelhez.

További információ az Exchange vészhelyreállítási folyamatról: [Exchange DAG-k](/Exchange/high-availability/database-availability-groups/database-availability-groups) és [Exchange-vészhelyreállítás.](/Exchange/high-availability/disaster-recovery/disaster-recovery)

## <a name="protect-sap"></a>Az SAP védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az SAP védelmét:

- Lehetővé teszi a helyszínen futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők az Azure-ba való replikálásával.
- Lehetővé teszi az Azure-t futtató SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők egy másik Azure-adatközpontba való replikálásával.
- Megkönnyíti a felhőre való áttelepítést, mivel az SAP üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
- Leegyszerűsíti az SAP projektek frissítését, tesztelését és prototípuskészítését, mivel a segítségével egy igény szerinti éles klón hozható létre az SAP alkalmazások teszteléséhez.

[További információ az](site-recovery-sap.md) SAP-vészhelyreállításról.

## <a name="protect-internet-information-services"></a>A Internet Information Services

A Site Recovery (IIS) Internet Information Services a következőképpen védheti meg az üzemelő példányokat:

Az Azure Site Recovery úgy gondoskodik a vészhelyreállításról, hogy a környezet kritikus összetevőit replikálja egy offline távoli helyre, vagy egy, a Microsoft Azure-hoz hasonló nyilvános felhőbe. Mivel a webkiszolgálóval és az adatbázissal együtt a virtuális gépek replikálva vannak a helyreállítási helyen, nincs szükség külön biztonsági mentésre a konfigurációs fájlokhoz vagy tanúsítványokhoz. A feladatátvétel után módosult környezeti változóktól függő alkalmazásleképezések és -kötések a vészhelyreállítás tervekbe integrált szkriptekkel frissíthetők. A virtuális gépek csak feladatátvétel során lesznek a helyreállítási helyen. Azure Site Recovery a teljes feladatátvétel vezénylében is segít a következő képességek biztosításával:

- A virtuális gépek leállításának és indításának sorrendbe állítása a különböző szinteken.
- Szkriptek hozzáadása, amelyek lehetővé teszik az alkalmazásfüggőségek és -kötések frissítését a virtuális gépeken az elindulás után. A szkriptek a DNS-kiszolgáló frissítésére is használhatók, hogy a helyreállítási helyre mutasson.
- Ip-címek lefoglalása a virtuális gépekhez a feladatátvétel előtt az elsődleges és a helyreállítási hálózatok leképezése és a feladatátvétel után nem frissíthető szkriptek használatával.
- Több webalkalmazás egykattintásos feladatátvételének képessége, amely kiküszöböli a katasztrófa során történő keveredés hatókörét.
- Lehetőség a helyreállítási tervek tesztelésére egy DR-részletezések számára elkülönített környezetben.

[További információ az](site-recovery-iis.md) IIS vészhelyreállítási folyamatról.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>A Citrix XenApp és a XenDesktop védelme

2020 márciusában a Citrix bejelentette a nyilvános felhőben üzemeltetett számítási feladatok elalasztását és támogatását. Ezért nem javasoljuk a Site Recovery a Citrix számítási feladatok védelméhez.

## <a name="next-steps"></a>Következő lépések

[További információ az](azure-to-azure-quickstart.md) Azure-beli virtuális gépek vészhelyreállításról.
