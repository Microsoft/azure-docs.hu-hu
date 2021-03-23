---
title: VMware Assessment-támogatás a Azure Migrate
description: Ismerje meg a VMware környezetben futó kiszolgálók értékelésének támogatását Azure Migrate felderítéssel és értékeléssel
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 943223ee99838b2b6d6de7eecb3c3b8c06d3562c
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773589"
---
# <a name="support-matrix-for-vmware-assessment"></a>A VMware Assessment támogatási mátrixa 

Ez a cikk az előfeltételeket és a támogatási követelményeket összegzi a VMware-környezetben futó kiszolgálók felderítésére és értékelésére az Azure-ba való áttelepítéshez a [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával. A kiszolgálók értékeléséhez létre kell hoznia egy projektet, amely hozzáadja a Azure Migrate: Discovery and Assessment eszközt a projekthez. Az eszköz hozzáadása után üzembe helyezi a Azure Migrate berendezést. A készülék folyamatosan felfedi a helyszíni kiszolgálókat, és konfigurációs és teljesítménybeli metaadatokat küld az Azure-nak. A felderítés befejezése után a felderített kiszolgálókat csoportokba gyűjtheti, és értékelést futtathat egy csoport számára.

Ha a VMware-kiszolgálókat át szeretné telepíteni az Azure-ba, tekintse át az [áttelepítési támogatási mátrixot](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Korlátozások

**Követelmény** | **Részletek**
--- | ---
**Projekt korlátai** | Egy Azure-előfizetésben több projektet is létrehozhat.<br/><br/> A VMware-környezetből akár 50 000-kiszolgálót is felderítheti és kivizsgálhat egyetlen [projektben](migrate-support-matrix.md#azure-migrate-projects). Egy projekt tartalmazhatja a fizikai kiszolgálókat és a Hyper-V környezetből származó kiszolgálókat is, az értékelési korlátokig.
**Felderítés** | Az Azure Migrate készülék legfeljebb 10 000 kiszolgálót tud felderíteni egy vCenter Server.
**Értékelés** | Egyetlen csoportban akár 35 000-kiszolgálót is hozzáadhat.<br/><br/> Egyetlen értékeléssel akár 35 000-kiszolgálót is megvizsgálhat.

[További](concepts-assessment-calculation.md) információ az értékelésekről.


## <a name="vmware-requirements"></a>VMware-követelmények

**VMware** | **Részletek**
--- | ---
**vCenter Server** | A felderíteni és értékelni kívánt kiszolgálókat a 5,5, 6,0, 6,5, 6,7 vagy 7,0 vCenter Server-es verzióval kell felügyelni.<br/><br/> A kiszolgálók az ESXi-gazdagép adatainak a berendezésben való felderítése jelenleg nem támogatott.
**Engedélyek** | Azure Migrate: a felderítéshez és értékeléshez vCenter Server írásvédett fiókra van szükség a felderítéshez és értékeléshez.<br/><br/> Ha a szoftverek leltározását és az ügynök nélküli függőségek elemzését szeretné elvégezni, a fióknak **Virtual Machines**  >  **vendég műveleteihez** engedélyezett jogosultságokkal kell rendelkeznie.

## <a name="server-requirements"></a>Kiszolgálókövetelmények
**VMware** | **Részletek**
--- | ---
**Operációs rendszer támogatott** | Az összes Windows-és Linux-operációs rendszer kiértékelése áttelepítésre lehetséges.
**Storage** | A SCSI-, IDE-és SATA-alapú vezérlőkhöz csatolt lemezek támogatottak.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-berendezés követelményei

A Azure Migrate a [Azure Migrate berendezést](migrate-appliance.md) használja a felderítéshez és értékeléshez. A készüléket a VMware-környezetben lévő kiszolgálóként helyezheti üzembe a vCenter Serverba importált vagy egy [PowerShell-parancsfájl](deploy-appliance-script.md)használatával.

- A VMware-es [készülékekre vonatkozó követelmények](migrate-appliance.md#appliance---vmware) ismertetése.
- Azure Government a készüléket [a szkript használatával](deploy-appliance-script-government.md)kell telepítenie.
- Tekintse át azokat az URL-címeket, amelyeket a készüléknek [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell elérnie.


## <a name="port-access-requirements"></a>A porthoz való hozzáférési követelmények

**Eszköz** | **Kapcsolat**
--- | ---
**Berendezés** | Bejövő kapcsolatok a 3389-as TCP-porton, hogy engedélyezze a távoli asztali kapcsolatokat a berendezéssel.<br/><br/> Bejövő kapcsolatok a 44368-as porton a berendezés-kezelő alkalmazás távoli eléréséhez az URL-cím használatával: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Kimenő kapcsolatok a 443-as porton (HTTPS), a felderítési és a teljesítménybeli metaadatok küldéséhez Azure Migrate.
**vCenter-kiszolgáló** | A 443-es TCP-porton bejövő kapcsolatok lehetővé teszik, hogy a berendezés konfigurációs és teljesítménybeli metaadatokat gyűjtsön az értékelésekhez. <br/><br/> A készülék alapértelmezés szerint az 443-as porton csatlakozik a vCenter-hez. Ha a vCenter-kiszolgáló egy másik portot figyel, akkor a felderítés beállításakor módosíthatja a portot.
**ESXi-gazdagépek** | Ha a [szoftverek leltározását vagy az](how-to-discover-applications.md) [ügynök nélküli függőségek elemzését](concepts-dependency-visualization.md#agentless-analysis)szeretné elvégezni, akkor a készülék a 443-as TCP-porton keresztül csatlakozik az ESXi-gazdagépekhez a szoftverek leltárának és függőségeinek felderítéséhez a kiszolgálókon.

## <a name="application-discovery-requirements"></a>Alkalmazás-felderítési követelmények

A kiszolgálók felderítése mellett Azure Migrate: a felderítés és értékelés a kiszolgálókon futó szoftveres leltár felderítését is képes észlelni. Az alkalmazások felderítése lehetővé teszi a helyszíni munkaterhelésekhez igazított áttelepítési útvonal azonosítását és megtervezését.

**Támogatás** | **Részletek**
--- | ---
**Támogatott kiszolgálók** | Jelenleg csak a VMware-környezetben található kiszolgálók esetében támogatott. Az alkalmazások felderítése akár 10000-kiszolgálón is elvégezhető minden Azure Migrate készülékről.
**Operációs rendszerek** | Az összes Windows-és Linux-verziót futtató kiszolgálók támogatottak.
**VIRTUÁLIS gépekre vonatkozó követelmények** | A szoftveres leltár felderítéséhez a VMware-eszközöket telepíteni és futtatni kell a kiszolgálókon. <br/><br/> A VMware-eszközök verziójának későbbinek kell lennie, mint 10.2.0.<br/><br/> A Windows-kiszolgálóknak telepítve kell lennie a PowerShell 2,0-es vagy újabb verziójával.
**Felderítés** | A kiszolgálókon az alkalmazások felderítése a kiszolgálókon telepített VMware-eszközök használatával történik a vCenter Server. A készülék a vSphere API-k használatával gyűjti össze a szoftver leltárával kapcsolatos információkat a vCenter Server. Az alkalmazások felderítése ügynök nélkül történik. Nincs ügynök telepítve a kiszolgálón, és a készülék nem csatlakozik közvetlenül a kiszolgálókhoz. A WMI-t és az SSH-t a Windows-és Linux-kiszolgálókon engedélyezni és elérhetővé kell tennie.
**felhasználói fiók vCenter Server** | Az értékeléshez használt vCenter Server írásvédett fióknak a **Virtual Machines** vendég műveleteire vonatkozó jogosultságokat kell biztosítania,  >  hogy együttműködjön a kiszolgálókkal az alkalmazások felderítése érdekében.
**Kiszolgáló-hozzáférés** | Az alkalmazások felderítéséhez több tartományt és tartományon kívüli (Windows/Linux) hitelesítő adatot is hozzáadhat a készülék Configuration Managerben.<br/><br/> Szüksége van egy vendég felhasználói fiókra a Windows-kiszolgálókhoz, valamint egy normál/normál felhasználói fiókot (nem sudo hozzáférés) az összes Linux-kiszolgálóhoz.
**Port-hozzáférés** | Az Azure Migrate készüléknek képesnek kell lennie csatlakozni az 443-es TCP-porthoz az ESXi-gazdagépeken futtató kiszolgálókon, amelyeken az alkalmazások felderítését el szeretné végezni. A vCenter Server ESXi-gazdagép kapcsolatát adja vissza, hogy letöltse a szoftver leltárának részleteit tartalmazó fájlt.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>SQL Server példányok és adatbázisok felderítésére vonatkozó követelmények

> [!Note]
> A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. A funkció kipróbálásához hozzon létre egy projektet a **Kelet-Ausztrália** régióban [**ezzel a hivatkozással**](https://aka.ms/AzureMigrate/SQL). Ha már van egy projektje Kelet-Ausztráliában, és szeretné kipróbálni ezt a funkciót, ellenőrizze, hogy eleget tett-e ezeknek az [**előfeltételeknek**](how-to-discover-sql-existing-project.md) a portálon.

Az [alkalmazás-felderítés](how-to-discover-applications.md) azonosítja a SQL Server példányokat. Ezen információk alapján a készülék a Windows-hitelesítéssel SQL Server vagy a készüléken megadott hitelesítő adatokkal kísérli meg a kapcsolódást a megfelelő SQL Server példányokhoz. A csatlakozás után a készülék összegyűjti SQL Server példányok és adatbázisok konfigurációs és teljesítményadatokat. A SQL Server konfigurációs adatai 24 óránként frissülnek, és a teljesítményadatokat 30 másodpercenként rögzíti a rendszer.

**Támogatás** | **Részletek**
--- | ---
**Támogatott kiszolgálók** | Jelenleg csak a VMware-környezetben található SQL-kiszolgálók támogatják. Akár 300 SQL Server példányt vagy 6000 SQL-adatbázist is felfedezhet, attól függően, hogy melyik a kisebb.
**Windows-kiszolgálók** | A Windows Server 2008-es és újabb verziók támogatottak.
**Linux-kiszolgálók** | Jelenleg nem támogatott.
**Hitelesítési mechanizmus** | A Windows és a SQL Server hitelesítés is támogatott. Mindkét hitelesítési típus hitelesítő adatait megadhatja a készülék Configuration Managerben.
**Az SQL Server hozzáférése** | Azure Migrate Windows felhasználói fiókot igényel, amely tagja a sysadmin kiszolgálói szerepkörnek.
**SQL Server-verziók** | A SQL Server 2008 és újabb verziók támogatottak.
**SQL Server kiadások** | A nagyvállalati, a standard, a fejlesztői és az expressz kiadások támogatottak.
**Támogatott SQL-konfiguráció** | Jelenleg csak önálló SQL Server példányok és a kapcsolódó adatbázisok felderítése támogatott.<br/> A feladatátvevő fürt és az Always On rendelkezésre állási csoportok azonosítása nem támogatott.
**Támogatott SQL-szolgáltatások** | Csak SQL Server-adatbázismotor támogatott. <br/> A SQL Server Reporting Services (SSRS), a SQL Server Integration Services (SSIS) és a SQL Server Analysis Services (SSAS) felderítése nem támogatott.

> [!Note]
> A Azure Migrate titkosítja Azure Migrate berendezés és a forrás SQL Server példányok közötti kommunikációt (a kapcsolat titkosítása tulajdonság értéke TRUE). Ezek a kapcsolatok a [**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) -mel vannak titkosítva (igaz értékre állítva); az átviteli réteg az SSL használatával titkosítja a csatornát, és megkerüli a tanúsítványláncot a megbízhatóság ellenőrzéséhez. A berendezés-kiszolgálót úgy kell beállítani, hogy [**megbízzon a tanúsítvány legfelső szintű hitelesítésszolgáltatóján**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Ha nincs tanúsítvány kiépítve a kiszolgálón az indításkor, SQL Server létrehoz egy önaláírt tanúsítványt, amelyet a rendszer a bejelentkezési csomagok titkosítására használ. [**További információ**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Függőségi elemzési követelmények (ügynök nélküli)

A függőségek [elemzése](concepts-dependency-visualization.md) segít az Azure-ba felmérni és áttelepíteni kívánt helyszíni kiszolgálók közötti függőségek azonosításában. A táblázat összefoglalja az ügynök nélküli függőségi elemzés beállításának követelményeit.

**Támogatás** | **Részletek**
--- | --- 
**Támogatott kiszolgálók** | Jelenleg csak a VMware-környezetben található kiszolgálók esetében támogatott.
**Windows-kiszolgálók** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64 bites).<br/>Microsoft Windows Server 2008 (32 bites).
**Linux-kiszolgálók** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 és újabb verziók.
**Kiszolgálókövetelmények** | Az elemezni kívánt kiszolgálókon telepíteni és futtatni kell a VMware-eszközöket (a 10.2.0 később).<br/><br/> A kiszolgálóknak telepítve kell lennie a PowerShell 2,0-es vagy újabb verziójával.
**Felderítési módszer** |  A kiszolgálók közötti függőségi információkat a rendszer a vCenter Server a kiszolgálóra telepített VMware-eszközök használatával gyűjti össze. A készülék a vSphere API-k használatával gyűjti össze az adatokat a vCenter Server. Nincs ügynök telepítve a kiszolgálón, és a készülék nem csatlakozik közvetlenül a kiszolgálókhoz. A WMI-t és az SSH-t a Windows-és Linux-kiszolgálókon engedélyezni és elérhetővé kell tennie.
**vCenter-fiók** | A Azure Migrate által az értékelési igényekhez használt írásvédett fióknak engedélyezve kell lennie **Virtual Machines > vendég műveleteihez**.
**Windows Server-engedélyek** |  A kiszolgálókon rendszergazdai engedélyekkel rendelkező felhasználói fiók (helyi vagy tartomány).
**Linux-fiók** | A root felhasználói fiók vagy egy, a/bin/netstat-és/bin/ls-fájlokra vonatkozó engedélyekkel rendelkező fiók: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.<br/><br/> A következő parancsokkal állíthatja be ezeket a képességeket: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat
**Port-hozzáférés** | A Azure Migrate készüléknek képesnek kell lennie csatlakozni az 443-es TCP-porthoz azon ESXi-gazdagépeken, amelyeken a felderíteni kívánt függőségeket futtató kiszolgálók futnak. A vCenter Server ESXi gazdagép-kapcsolódást ad vissza a függőségi adatok tárolására szolgáló fájl letöltéséhez.


## <a name="dependency-analysis-requirements-agent-based"></a>Függőségi elemzési követelmények (ügynök-alapú)

A függőségek [elemzése](concepts-dependency-visualization.md) segít az Azure-ba felmérni és áttelepíteni kívánt helyszíni kiszolgálók közötti függőségek azonosításában. A táblázat összefoglalja az ügynök-alapú függőségi elemzés beállításának követelményeit.

**Követelmény** | **Részletek** 
--- | --- 
**Üzembe helyezés előtt** | Rendelkeznie kell egy, a projekthez hozzáadott Azure Migrate: felderítési és értékelési eszközzel.<br/><br/>  A függőségi vizualizációt egy Azure Migrate berendezés beállítása után telepítheti a helyszíni kiszolgálók felderítése érdekében.<br/><br/> [Ismerje meg, hogyan](create-manage-projects.md) hozhat létre egy projektet első alkalommal.<br/> [Ismerje meg, hogyan](how-to-assess.md) adhat hozzá egy felderítési és értékelési eszközt egy meglévő projekthez.<br/> Ismerje meg, hogyan állíthatja be a Azure Migrate készüléket a [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)vagy fizikai kiszolgálók értékeléséhez.
**Támogatott kiszolgálók** | A helyszíni környezetben lévő összes kiszolgáló esetében támogatott.
**Naplóelemzés** | A Azure Migrate a [Service Map](../azure-monitor/insights/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/log-query/log-query-overview.md) a függőségi vizualizációhoz.<br/><br/> Új vagy meglévő Log Analytics munkaterületet társít egy projekthez. A projekt munkaterülete nem módosítható a hozzáadása után. <br/><br/> A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a projektnek.<br/><br/> A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. Más régiókban lévő munkaterületek nem társíthatók projekthez.<br/><br/> A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Log Analytics a Azure Migratehoz társított munkaterület a projekt kulcsával és a projekt nevével van címkézve.
**Szükséges ügynökök** | Az elemezni kívánt összes kiszolgálón telepítse a következő ügynököket:<br/><br/> A [Microsoft monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> A [függőségi ügynök](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Ha a helyszíni kiszolgálók nem kapcsolódnak az internethez, le kell töltenie és telepítenie kell Log Analytics-átjárót.<br/><br/> További információ a [függőségi ügynök](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) és az [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)telepítéséről.
**Log Analytics munkaterület** | A munkaterületnek ugyanahhoz az előfizetéshez kell tartoznia, mint a projektnek.<br/><br/> Azure Migrate az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található munkaterületeket támogatja.<br/><br/>  A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> A projekt munkaterülete nem módosítható a hozzáadása után.
**Költségek** | Az Service Map-megoldás nem számít fel díjat az első 180 napra (az Log Analytics munkaterület projekthez való hozzárendelésének napjától számítva)/<br/><br/> A 180 nap elteltével a standard Log Analytics-díjszabás vonatkozik rá.<br/><br/> A társított Log Analytics-munkaterületen a Service Maptól eltérő bármely megoldás használata esetén a Log Analytics [standard díjait](https://azure.microsoft.com/pricing/details/log-analytics/) kell fizetnie.<br/><br/> A projekt törlésekor a munkaterület nem törlődik vele együtt. A projekt törlését követően a Service Map használat nem ingyenes, és az egyes csomópontok a Log Analytics munkaterület fizetős szintjének megfelelően lesznek felszámítva.<br/><br/>Ha olyan projektekkel rendelkezik, amelyeket a Azure Migrate általános elérhetősége előtt hozott létre (GA-28. február 2018.), előfordulhat, hogy további Service Map díjat is felmerült. Ahhoz, hogy a fizetés csak 180 nap után legyen elérhető, javasoljuk, hogy hozzon létre egy új projektet, mivel a már meglévő munkaterületek továbbra is díjkötelesek.
**Felügyelet** | Amikor ügynököt regisztrál a munkaterületre, a projekt által megadott azonosítót és kulcsot használja.<br/><br/> A Log Analytics munkaterületet Azure Migraten kívül is használhatja.<br/><br/> Ha törli a társított projektet, a munkaterület nem törlődik automatikusan. [Törölje manuálisan](../azure-monitor/platform/manage-access.md).<br/><br/> Ne törölje a Azure Migrate által létrehozott munkaterületet, hacsak nem törli a projektet. Ha így tesz, a függőségi vizualizáció funkció nem a várt módon fog működni.
**Internetkapcsolat** | Ha a kiszolgálók nem csatlakoznak az internethez, akkor telepítenie kell az Log Analytics-átjárót.
**Azure Government** | Az ügynök-alapú függőség elemzése nem támogatott.


## <a name="next-steps"></a>Következő lépések

- [Tekintse át](best-practices-assessment.md) az értékelések létrehozásával kapcsolatos ajánlott eljárásokat.
- [Felkészülés a VMware](./tutorial-discover-vmware.md) -értékelésre.