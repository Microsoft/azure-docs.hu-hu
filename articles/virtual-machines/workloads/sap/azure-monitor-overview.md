---
title: Azure Monitor for SAP Solutions áttekintés és architektúra| Microsoft Docs
description: Ez a cikk az SAP-megoldásokhoz használt Azure Monitorral kapcsolatos gyakori kérdésekre ad választ
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: c561a9a786765ccfdaf00abf4e0d9c8cc550cb9a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377208"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor SAP-megoldásokhoz (előzetes verzió)

## <a name="overview"></a>Áttekintés

Azure Monitor for SAP Solutions azure-natív monitorozási termék az ügyfelek számára, amely SAP-környezeteket futtat az Azure-ban. A termék nagy méretű [Azure-beli SAP Virtual Machines](./hana-get-started.md) Azure-beli SAP [is működik.](./hana-overview-architecture.md)
A Azure Monitor for SAP Solutions segítségével az ügyfelek egyetlen központi helyen gyűjthet telemetriai adatokat az Azure-infrastruktúrából és -adatbázisokból, és vizuálisan korrelálhatja a telemetriai adatokat a gyorsabb hibaelhárítás érdekében.

Azure Monitor for SAP Solutions szolgáltatás a Azure Marketplace. Egyszerű, intuitív beállítási felületet biztosít, és mindössze néhány kattintással üzembe helyezheti az erőforrást a Azure Monitor for SAP Solutions (más néven **SAP Monitor-erőforrás).**

Az ügyfelek az adott összetevőhöz tartozó megfelelő szolgáltató hozzáadásával figyelheti az SAP-környezet különböző összetevőit, például az Azure Virtual Machines-t, a magas rendelkezésre állású fürtöt, az SAP HANA-adatbázist, az SAP NetWeavert stb. 

Támogatott infrastruktúra:

- Azure-beli virtuális gép
- Nagyméretű Azure-példány

Támogatott adatbázisok:
- SAP HANA-adatbázis
- Microsoft SQL Server

Azure Monitor for SAP Solutions meglévő funkciók, például [a](../../../azure-monitor/overview.md) Log Analytics Azure Monitor a [Munkafüzetek](../../../azure-monitor/visualize/workbooks-overview.md) segítségével további monitorozási képességeket biztosít. Az ügyfelek egyéni vizualizációkat hozhatnak létre az Azure Monitor for SAP Solutions által biztosított [](../../../azure-monitor/alerts/tutorial-response.md) alapértelmezett munkafüzetek szerkesztésével, egyéni lekérdezések [](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) írásával és egyéni riasztások létrehozásával az Azure Log Analytics-munkaterület használatával, a rugalmas megőrzési időtartam előnyeinek kihasználása és a monitorozási adatok csatlakoztatása a jegykiadó rendszerhez. [](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) [](../../../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Milyen adatokat gyűjt Azure Monitor SAP-megoldásokhoz?

Az adatok Azure Monitor for SAP Solutions az ügyfelek által konfigurált szolgáltatóktól függ. A nyilvános előzetes verzióban a következő adatok gyűjtése folyamatban van.

Magas rendelkezésre állású Pacemaker-fürt telemetria:
- Csomópont-, erőforrás- és SBD-eszközállapot
- Pacemaker helykorlátozásai
- Kvórumszavazatok és körállapot
- [Továbbiak](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetria:
- PROCESSZOR-, memória-, lemez- és hálózathasználat
- HANA rendszerreplikáció (HSR)
- HANA biztonsági mentése
- HANA-gazdagép állapota
- Indexkiszolgáló és névkiszolgálói szerepkörök

Microsoft SQL Server-telemetria:
- Processzor, memória, lemezkihasználtság
- Gazdagépnév, SQL-példány neve, SAP-rendszerazonosító
- Kötegelt kérések, fordítások és lap várható élettartama az idő során
- A 10 legdrágább SQL-utasítás idővel
- Az SAP-rendszer első 12 legnagyobb táblája
- A hibanaplóban rögzített SQL Server problémák
- Folyamatok blokkolása és SQL várakozási statisztika az idő során

Operációsrendszer-telemetria (Linux) 
- CPU-kihasználtság, el forkszám, futó és blokkolt folyamatok. 
- Memóriahasználat és elosztás a kihasznált, gyorsítótárazott és pufferelt erőforrások között. 
- Felcserélés kihasználtsága, lapozási és felcserélési arány. 
- Fájlrendszerek kihasználtsága, Olvasott és írt bájtok száma blokkeszközönként. 
- Olvasási/írási késés blokkeszközönként. 
- Folyamatos I/O-szám, állandó memória olvasási/írási bájtok. 
- Bejövő/kimenő hálózati csomagok, Hálózati bájtok be- és kimenő 

SAP NetWeaver telemetria:

- AZ SAP-rendszer és az alkalmazáskiszolgáló rendelkezésre állása, beleértve a Dispatcher, az ICM, az átjáró, az üzenetkiszolgáló, az Enqueue Server, az IGS Watchdog példányfolyamatok rendelkezésre állását
- A munkafolyamat kihasználtságának statisztikái és trendjei
- Lock-statisztikák és -trendek sorba való besorolása
- Várólista kihasználtságának statisztikái és trendjei

## <a name="data-sharing-with-microsoft"></a>Adatmegosztás a Microsofttal

Azure Monitor for SAP Solutions gyűjti a rendszer metaadatait, hogy továbbfejlesztett támogatást nyújtson az Azure-beli SAP számára. A piI/EUII nem gyűjthető be.
Az ügyfelek engedélyezhetik az adatmegosztást a Microsofttal a  Azure Monitor for SAP Solutions létrehozásakor, ha a legördülő menü Megosztás lehetőségét választja.
Erősen ajánlott, hogy az ügyfelek engedélyezték az adatmegosztást, mivel az több információt nyújt a Microsoft támogatási és mérnöki csapatának az ügyfélkörnyezetről, és jobb támogatást nyújt a kritikus fontosságú Azure-beli SAP számára.

## <a name="architecture-overview"></a>Az architektúra áttekintése

A következő ábra magas szinten azt mutatja be, Azure Monitor for SAP Solutions hogyan gyűjt telemetriai adatokat a SAP HANA adatbázisból. Az architektúra független attól, hogy a SAP HANA Azure-beli virtuális Virtual Machines azure-példányokra van-e telepítve.

![Azure Monitor SAP-megoldások architektúrája](./media/azure-monitor-sap/azure-monitor-architecture.png)

Az architektúra fő összetevői a következőek:
- Azure Portal – az ügyfelek kiindulási pontja. Az ügyfelek a piactérre navigálnak a Azure Portal és felfedezhetik a Azure Monitor for SAP Solutions
- Azure Monitor for SAP Solutions erőforrás – az ügyfelek számára a monitorozási telemetria megtekintésének kezdőlapja
- Felügyelt erőforráscsoport – automatikusan üzembe helyezhető a Azure Monitor for SAP Solutions részeként. A felügyelt erőforráscsoportban üzembe helyezett erőforrások segítenek a telemetria gyűjtésében. Az üzembe helyezett legfontosabb erőforrások és azok rendeltetése a következő:
   - Azure-beli virtuális gép: más néven *gyűjtő virtuális gép.* Ez egy Standard_B2ms virtuális gép. A virtuális gép fő célja a monitorozási *hasznos adatok gazdagépe.* A hasznos adatok monitorozása a telemetria forrásrendszerekből való gyűjtésének logikáját jelenti, és az összegyűjtött adatokat a monitorozási keretrendszerbe továbbítja. A fenti ábrán a monitorozási hasznos adatok azt a logikát tartalmazják, amely az sql SAP HANA-porton keresztül csatlakozik az adatbázishoz.
   - [Azure Key Vault:](../../../key-vault/general/basic-concepts.md)Ez az erőforrás az adatbázis SAP HANA hitelesítő adatainak biztonságos tárolására és a szolgáltatókra vonatkozó információk tárolására [van telepítve.](./azure-monitor-providers.md)
   - Log Analytics-munkaterület: az a cél, ahol a telemetriai adatok találhatók.
      - A vizualizáció a Log Analytics telemetriai adataira épül az [Azure Workbooks használatával.](../../../azure-monitor/visualize/workbooks-overview.md) Az ügyfelek testreszabhatják a vizualizációt. Az ügyfelek a munkafüzeteket vagy a munkafüzetek adott vizualizációit az Azure-irányítópultra is rögzítheti, így az automatikus javítás 30 perces minimális részletességgel is elérhető.
      - Az ügyfelek az SAP Monitor-erőforrással azonos előfizetésben lévő meglévő munkaterületüket is használhatjak, ha ezt a lehetőséget választják az üzembe helyezéskor.
      - Az ügyfelek a Kusto lekérdezési nyelv [](../../../azure-monitor/logs/log-query-overview.md) (KQL) használatával lekérdezéseket futtatnak a nyers táblákon a Log Analytics-munkaterületen belül. Egyéni *naplók megtekintése.*

> [!Note]
> Az ügyfelek felelnek a felügyelt erőforráscsoportban üzembe helyezett virtuális gép javításáért és karbantartásáért.

> [!Tip]
> Az ügyfelek dönthetnek úgy, hogy meglévő Log Analytics-munkaterületet használnak a telemetriagyűjtéshez, ha az ugyanabban az Azure-előfizetésben van telepítve, mint az erőforrás a Azure Monitor for SAP Solutions.

### <a name="architecture-highlights"></a>Az architektúra legfontosabb eseményei

Az architektúra legfontosabb kiemelve a következőket tartalmazza:
 - **Többpéldányos** – Az ügyfelek egy adott összetevőtípus (például HANA DB, HA fürt, Microsoft SQL Server, SAP NetWeaver) több SAP SID-jét is figyelheti egy virtuális hálózat egyetlen erőforrásával Azure Monitor for SAP Solutions.
 - **Több szolgáltató –** A fenti architektúradiagramon a SAP HANA látható példaként. Hasonlóképpen, az ügyfelek további szolgáltatókat konfigurálhat a megfelelő összetevőkhöz (például HANA-adatbázis, HA-fürt, Microsoft SQL-kiszolgáló, SAP NetWeaver) az összetevők adatainak gyűjtéséhez.
 - **Nyílt forráskód** – A Azure Monitor for SAP Solutions a [GitHubon érhető el.](https://github.com/Azure/AzureMonitorForSAPSolutions) Az ügyfelek a szolgáltatói kódra hivatkozva további információt kaphatnak a termékről, közreműködhet vagy megoszthatják a visszajelzéseket.
 - **Extensible query framework (Extensible Query Framework)** – A telemetriai adatok gyűjtésére vonatkozó SQL-lekérdezések [JSON-fájlban vannak megírva.](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) További SQL-lekérdezések egyszerűen hozzáadhatók további telemetriai adatok gyűjtéséhez. Az ügyfelek konkrét telemetriai adatokat kérhetnek a Azure Monitor for SAP Solutions, ha a dokumentum végén található hivatkozáson keresztül visszajelzést adnak, vagy kapcsolatba lépnek a fiók ügyfélszolgálatával.

## <a name="pricing"></a>Díjszabás
Azure Monitor for SAP Solutions ingyenes termék (licencdíj nélkül). Az ügyfelek felelnek a felügyelt erőforráscsoport mögöttes összetevőinek költségeiért.

## <a name="next-steps"></a>Következő lépések

Megismeri a szolgáltatókat, és létrehozhatja az Azure Monitor for SAP Solutions erőforrását.
 - További információ a [szolgáltatókról](./azure-monitor-providers.md)
 - [Sap Azure Monitor megoldások üzembe helyezése Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Kérdése van a Azure Monitor for SAP Solutions? Tekintse meg a [GYAKORI KÉRDÉSEK szakaszt](./azure-monitor-faq.md)
