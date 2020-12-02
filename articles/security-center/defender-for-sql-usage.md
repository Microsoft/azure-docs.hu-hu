---
title: Az Azure Defender használata az SQL-hez
description: Ismerje meg, hogyan használhatja a Azure Security Center opcionális Azure Defender for SQL-csomagot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2020
ms.author: memildin
ms.openlocfilehash: 07a94685fb3655aa5aa21a8658b9d975c764daff
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512278"
---
# <a name="azure-defender-for-sql-servers-on-machines"></a>Azure Defender a gépeken futó SQL-kiszolgálókon 

Ez az Azure Defender-csomag olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

A riasztások akkor jelennek meg, ha gyanús adatbázis-tevékenységek, potenciális sebezhetőségek vagy SQL-injektálási támadások, valamint rendellenes adatbázis-hozzáférés és lekérdezési minták vannak.

## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|A **gépeken futó Azure Defender szolgáltatás** számlázása [a díjszabási oldalon](security-center-pricing.md) látható módon történik.|
|Védett SQL-verziók:|Azure SQL Server (a Microsoft támogatási szolgálata által érintett összes verzió)|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) US Gov<br>![Nem](./media/icons/no-icon.png) Kínai gov, egyéb gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Azure Defender beállítása a gépeken futó SQL-kiszolgálókhoz

A csomag engedélyezése:

* A Log Analytics ügynök kiépítése az SQL Server gazdagépén. Ez biztosítja az Azure-hoz való kapcsolódást.

* Engedélyezze a választható csomagot Security Center díjszabási és beállítási lapján.

A fentiek mindegyikét alább találja.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>1. lépés A Log Analytics-ügynök kiépítése az SQL Server gazdagépén:

- **SQL Server Azure** -beli virtuális gépen – ha az SQL-gépe Azure-beli virtuális gépen üzemel, akkor [engedélyezheti a log Analytics- <a name="auto-provision-mma"></a> ügynök automatikus](security-center-enable-data-collection.md#auto-provision-mma)kihelyezését. Azt is megteheti, hogy a manuális eljárást követve bevezeti [a Azure stack virtuális gépeket](quickstart-onboard-machines.md#onboard-your-azure-stack-vms).
- **SQL Server az Azure-on** – ha az SQL Servert az [Azure arc](../azure-arc/index.yml) -kompatibilis kiszolgálók felügyelik, a log Analytics-ügynököt a Security Center javaslat használatával telepítheti: "log Analytics ügynökének telepítve kell lennie a Windows-alapú Azure arc-gépeken (előzetes verzió)". Másik lehetőségként az [Azure arc dokumentációjában](../azure-arc/servers/manage-vm-extensions.md)ismertetett telepítési módszereket is követheti.

- **Helyszíni SQL Server** – ha a SQL Server Azure arc nélküli helyszíni Windows-gépen üzemel, két lehetőség közül választhat az Azure-hoz való csatlakozáshoz:
    
    - Az **Azure arc üzembe helyezése** – bármely Windows-gépet összekapcsolhat Security Centerhoz. Az Azure arc azonban mélyebb integrációt tesz lehetővé az *összes* Azure-környezetben. Ha beállítja az Azure-ívet, a portálon megjelenik a **SQL Server – Azure arc** oldal, és a biztonsági riasztások az oldal dedikált **Biztonság** lapján jelennek meg. Ezért az első és ajánlott lehetőség az [Azure arc beállítása a gazdagépen](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) , és kövesse az Azure-beli **SQL Server vonatkozó** utasításokat.
        
    - **A Windows rendszerű gép összekapcsolása az Azure arc nélkül** – ha úgy dönt, hogy a Windows-gépen futó SQL Servert az Azure arc használata nélkül kívánja összekapcsolni, kövesse a Windows rendszerű [gépek összekapcsolása a Azure monitor](../azure-monitor/platform/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>2. lépés Engedélyezze a választható csomagot Security Center díjszabási és beállítási lapján:

1. A Security Center menüjében nyissa meg a **díjszabási & beállítások** lapot.

    - Ha **Azure Security Center alapértelmezett munkaterületét** használja ("alapértelmezettmunkaterület-[előfizetési azonosító]-[régió]"), válassza ki a megfelelő **előfizetést**.

    - Ha **nem alapértelmezett munkaterületet** használ, válassza ki a megfelelő **munkaterületet** (ha szükséges, adja meg a munkaterület nevét a szűrőben):

        ![A nem alapértelmezett munkaterület megkeresése cím szerint](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Állítsa be az **Azure Defender for SQL Servers for Machines** szolgáltatást **a következőre:.** 

    ![Security Center díjszabási oldal opcionális csomagokkal](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)

    A csomag a kiválasztott munkaterülethez csatlakoztatott összes SQL-kiszolgálón engedélyezve lesz. A védelem teljes mértékben aktív lesz az SQL Server példány első újraindítása után.

    >[!TIP] 
    > Új munkaterület létrehozásához kövesse az [log Analytics-munkaterület létrehozása](../azure-monitor/learn/quick-create-workspace.md)című témakör utasításait.


1. Szükség esetén az e-mail-értesítések konfigurálása a biztonsági riasztásokhoz. 
    Megadhatja a címzettek listáját, amely e-mailben értesítést kap a Security Center riasztások létrehozásakor. Az e-mail tartalmaz egy közvetlen SK-t a riasztáshoz Azure Security Center az összes vonatkozó részletet. További információ: [e-mail-értesítések beállítása biztonsági riasztásokhoz](security-center-provide-security-contact-details.md).



## <a name="explore-vulnerability-assessment-reports"></a>A sebezhetőségi felmérési jelentések megismerése

A sebezhetőség-felmérési szolgáltatás hetente egyszer vizsgálja meg az adatbázisokat. A vizsgálatok a hét azon napján futnak, amelyen engedélyezte a szolgáltatást.

A sebezhetőségi felmérés irányítópultja áttekintést nyújt az összes adatbázisra vonatkozó értékelési eredményekről, valamint az egészséges és a nem kifogástalan adatbázisok összefoglalásáról, valamint a kockázatos eloszlások szerinti sikertelen ellenőrzések általános összegzéséről.

A sebezhetőségi felmérés eredményei közvetlenül a Security Center tekinthetők meg.

1. A Security Center oldalsávján nyissa meg a **javaslatok** lapot, és válassza ki a **számítógépeken található SQL-kiszolgálók ajánlott biztonsági réseit (előzetes verzió)**. További információ: [Security Center javaslatok](security-center-recommendations.md). 

    :::image type="content" source="./media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png" alt-text="A biztonsági rések felmérésének eredményeit a gépeken lévő SQL-kiszolgálókon szervizelni kell (előzetes verzió)":::

    Megjelenik a javaslat részletes nézete.

    :::image type="content" source="./media/security-center-advanced-iaas-data/all-servers-view.png" alt-text="A javaslat részletes nézete":::

1. További részleteket a következő részletezésben talál:

    * A beolvasott erőforrások (adatbázisok) és a tesztelt biztonsági ellenőrzések listájának áttekintéséhez válassza ki az Önt érdeklő kiszolgálót.

    * Az adott SQL-adatbázis által csoportosított biztonsági rések áttekintéséhez válassza ki az Önt érdeklő adatbázist.

    Az egyes nézetekben a biztonsági ellenőrzések **Súlyosság** szerint rendezve jelennek meg. Egy adott biztonsági vizsgálatra kattintva megtekintheti a részleteket tartalmazó ablaktáblát **, amely** **leírja, hogyan** javíthatja, és egyéb kapcsolódó információkat, például a **hatást** vagy a **teljesítménytesztet**.

## <a name="azure-defender-for-sql-alerts"></a>SQL-alapú Azure Defender-riasztások
A riasztásokat szokatlan és potenciálisan ártalmas kísérletek generálják az SQL-gépek eléréséhez vagy kiaknázásához. Ezek az események riasztásokat indíthatnak a riasztások [SQL Database és az Azure szinapszis Analytics szakaszban, a riasztások hivatkozása lapon](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Biztonsági riasztások feltárása és kivizsgálása

Az Azure Defender for SQL-riasztások Security Center riasztások lapján, az erőforrás Biztonság lapján, az [Azure Defender irányítópultján](azure-defender-dashboard.md)vagy a riasztási e-mailek közvetlen hivatkozásán keresztül érhetők el.

1. A riasztások megtekintéséhez válassza a **biztonsági riasztások** lehetőséget Security Center menüjében, és válassza ki a riasztást.

1. A riasztásokat önállóan, részletes szervizelési lépésekkel és vizsgálati információkkal tervezték meg. Részletesebben is megvizsgálhat további Azure Security Center és Azure Sentinel-képességeket a szélesebb körű nézet érdekében:

    * A további vizsgálatok érdekében engedélyezze SQL Server naplózási funkcióját. Ha Ön Azure Sentinel-felhasználó, feltöltheti az SQL-naplózási naplókat a Windows biztonsági napló eseményeiből a Sentinel szolgáltatásba, és élvezheti a részletes nyomozást. [További információ a SQL Server naplózásról](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * A biztonsági helyzet javításához használja Security Center javaslatait az egyes riasztásokban jelzett gazdagéphez. Ez csökkenti a jövőbeli támadások kockázatát. 

    [További információ a riasztások kezeléséről és megválaszolásáról](security-center-managing-and-responding-alerts.md).


## <a name="next-steps"></a>Következő lépések

Kapcsolódó anyagok esetében tekintse meg a következő cikket:

- [Biztonsági riasztások SQL Database és az Azure szinapszis Analytics szolgáltatáshoz](alerts-reference.md#alerts-sql-db-and-warehouse)
- [E-mail-értesítések beállítása biztonsági riasztásokhoz](security-center-provide-security-contact-details.md)
- [További információ az Azure Sentinelről](../sentinel/index.yml)
- [Azure Security Center adatbiztonsági csomagja](../azure-sql/database/azure-defender-for-sql.md)