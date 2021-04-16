---
title: Azure Monitor for SAP Solutions áttekintése és architektúrája| Microsoft Docs
description: Ez a cikk az SAP-megoldásokhoz használt Azure Monitorral kapcsolatos gyakori kérdésekre ad választ
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 45085c910974402a968075a66087a04fb30e8bd9
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576203"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor SAP-megoldásokhoz (előzetes verzió)

## <a name="overview"></a>Áttekintés

Azure Monitor for SAP Solutions azure-beli natív monitorozási termék az ügyfelek számára, amely SAP-környezetet futtat az Azure-ban. A termék nagy [](./hana-get-started.md) méretű Azure-beli SAP Virtual Machines [Azure-beli SAP is működik.](./hana-overview-architecture.md)
A Azure Monitor for SAP Solutions lehetővé teszi, hogy az ügyfelek egyetlen központi helyen gyűjtsenek telemetriai adatokat az Azure-infrastruktúrából és -adatbázisokból, és vizuálisan korrelálják a telemetriai adatokat a gyorsabb hibaelhárítás érdekében.

Azure Monitor for SAP Solutions a szolgáltatás Azure Marketplace. Egyszerű, intuitív beállítási felületet biztosít, és mindössze néhány kattintással üzembe helyezheti az erőforrást a Azure Monitor for SAP Solutions (más néven **SAP Monitor-erőforrás).**

Az ügyfelek az SAP-környezet különböző összetevőit figyelheti, például az Azure Virtual Machines-t, a magas rendelkezésre állású fürtöt, az SAP HANA-adatbázist, az SAP NetWeavert stb. az adott összetevőhöz tartozó szolgáltató hozzáadásával. 

Támogatott infrastruktúra:

- Azure-beli virtuális gép
- Nagyméretű Azure-példány

Támogatott adatbázisok:
- SAP HANA-adatbázis
- Microsoft SQL Server

Azure Monitor for SAP Solutions a meglévő funkciók, [például a](../../../azure-monitor/overview.md) Log Analytics Azure Monitor a [Munkafüzetek](../../../azure-monitor/visualize/workbooks-overview.md) segítségével további figyelési képességeket biztosít. Az ügyfelek egyéni vizualizációkat hozhatnak létre az Azure Monitor for SAP Solutions által biztosított [](../../../azure-monitor/alerts/tutorial-response.md) alapértelmezett munkafüzetek szerkesztésével, egyéni lekérdezések [](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) írásával és egyéni riasztások létrehozásával az Azure Log Analytics-munkaterület használatával, a rugalmas megőrzési időtartam előnyeinek kihasználása és a monitorozási adatok összekapcsolása a jegykiadó rendszerrel. [](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) [](../../../azure-monitor/logs/log-analytics-tutorial.md)

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
- Indexkiszolgálói és névkiszolgálói szerepkörök

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

Azure Monitor for SAP Solutions gyűjti a rendszer metaadatait, hogy jobb támogatást nyújtson az Azure-beli SAP számára. A piI/EUII nem gyűjthető be.
Az ügyfelek engedélyezhetik az adatmegosztást a Microsofttal a  Azure Monitor for SAP Solutions létrehozásakor a Megosztás lehetőség kiválasztásával a legördülő menüből.
Erősen ajánlott, hogy az ügyfelek engedélyezték az adatmegosztást, mivel az több információt nyújt a Microsoft támogatási és mérnöki csapatának az ügyfélkörnyezetről, és jobb támogatást nyújt a kritikus fontosságú Azure-beli SAP számára.

## <a name="architecture-overview"></a>Az architektúra áttekintése

A következő ábra magas szinten azt mutatja be, Azure Monitor for SAP Solutions hogyan gyűjt telemetriai adatokat SAP HANA adatbázisból. Az architektúra független attól, hogy a SAP HANA Azure-beli virtuális Virtual Machines azure-példányokra van-e telepítve.

![Azure Monitor SAP-megoldások architektúrája](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Az architektúra fő összetevői a következőek:
- Azure Portal – kiindulási pont az ügyfelek számára. Az ügyfelek a piactérre navigálva Azure Portal, és felfedezhetik a Azure Monitor for SAP Solutions
- Azure Monitor for SAP Solutions erőforrás – kezdőlap az ügyfelek számára a monitorozási telemetria megtekintéséhez
- Felügyelt erőforráscsoport – automatikusan üzembe helyezhető a Azure Monitor for SAP Solutions részeként. A felügyelt erőforráscsoportban üzembe helyezett erőforrások segítenek a telemetria gyűjtésében. Az üzembe helyezett legfontosabb erőforrások és rendeltetésük a következő:
   - Azure-beli virtuális gép: más néven *gyűjtő virtuális gép.* Ez egy Standard_B2ms virtuális gép. A virtuális gép fő célja a monitorozási *hasznos adatok gazdagépe.* A hasznos adatok monitorozása a telemetria forrásrendszerekből való gyűjtésének logikáját jelenti, és az összegyűjtött adatokat a monitorozási keretrendszerbe továbbítja. A fenti ábrán a monitorozási hasznos adatok azt a logikát tartalmazják, amely az sql SAP HANA-porton keresztül csatlakozik az adatbázishoz.
   - [Azure Key Vault:](../../../key-vault/general/basic-concepts.md)Ezt az erőforrást úgy telepítik, hogy biztonságosan tárolja SAP HANA adatbázis hitelesítő adatait és a szolgáltatókkal kapcsolatos [információkat.](./azure-monitor-providers.md)
   - Log Analytics-munkaterület: az a cél, ahol a telemetriai adatok találhatók.
      - A vizualizáció a Log Analytics telemetriai adataira épül [az Azure-munkafüzetek használatával.](../../../azure-monitor/visualize/workbooks-overview.md) Az ügyfelek testreszabhatják a vizualizációt. Az ügyfelek a munkafüzeteiket vagy a Munkafüzetek adott vizualizációit is rögzíthetik az Azure-irányítópulton az automatikus javítás érdekében, a legalacsonyabb 30 perces részletességgel.
      - Az ügyfelek az SAP Monitor-erőforrással azonos előfizetésben lévő meglévő munkaterületüket is használhatjak, ha ezt a lehetőséget választják az üzembe helyezéskor.
      - Az ügyfelek a Kusto lekérdezési nyelv [](../../../azure-monitor/logs/log-query-overview.md) (KQL) használatával lekérdezéseket futtatnak a Log Analytics-munkaterületen belüli nyers táblákon. Egyéni *naplók megtekintése.*

> [!Note]
> Az ügyfelek felelnek a felügyelt erőforráscsoportban üzembe helyezett virtuális gép javításáért és karbantartásáért.

> [!Tip]
> Az ügyfelek dönthetnek úgy, hogy egy meglévő Log Analytics-munkaterületet használnak a telemetriai adatok gyűjtéséhez, ha a telemetriai adatok erőforrásával azonos Azure-előfizetésben vannak Azure Monitor for SAP Solutions.

### <a name="architecture-highlights"></a>Az architektúra legfontosabb eseményei

Az architektúra legfontosabb fontos kiemelve a következő:
 - **Többpéldányos** – Az ügyfelek egy adott összetevőtípus (például HANA-adatbázis, HA-fürt, Microsoft SQL Server, SAP NetWeaver) több SAP SID-jét figyelheti egy VNET-en belül egyetlen erőforrással Azure Monitor for SAP Solutions.
 - **Több szolgáltató –** A fenti architektúradiagram példaként SAP HANA szolgáltatót. Hasonlóképpen, az ügyfelek további szolgáltatókat konfigurálhat a megfelelő összetevőkhöz (például HANA DB, HA-fürt, Microsoft SQL Server, SAP NetWeaver), hogy adatokat gyűjtsenek az összetevőkről.
 - **Nyílt forráskód** – Az alkalmazás forráskódja Azure Monitor for SAP Solutions a [GitHubon érhető el.](https://github.com/Azure/AzureMonitorForSAPSolutions) Az ügyfelek a szolgáltatói kódra hivatkozva további információt kaphatnak a termékről, illetve visszajelzéseket küldhetnek vagy oszthatnak meg.
 - **Extensible query framework (Extensible query framework)** – A telemetriai adatok gyűjtésére vonatkozó SQL-lekérdezések [JSON-fájlban vannak megírva.](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) További SQL-lekérdezések egyszerűen hozzáadhatók további telemetriai adatok gyűjtéséhez. Az ügyfelek kérhetik, hogy adott telemetriai adatokat vegyenek fel a Azure Monitor for SAP Solutions, ha a dokumentum végén található hivatkozáson keresztül visszajelzést adnak, vagy kapcsolatba lépnek a fiók ügyfélszolgálatával.

## <a name="pricing"></a>Díjszabás
Azure Monitor for SAP Solutions ingyenes termék (licencdíj nélkül). Az ügyfelek felelnek a felügyelt erőforráscsoportban lévő mögöttes összetevők költségeinek kifizetéséért.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a szolgáltatókat, és hozza létre Azure Monitor for SAP Solutions erőforrását.
 - További információ a [szolgáltatókról](./azure-monitor-providers.md)
 - [Sap Azure Monitor megoldások üzembe helyezése Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Kérdése van a Azure Monitor for SAP Solutions? Tekintse meg a [GYAKORI KÉRDÉSEK](./azure-monitor-faq.md) szakaszt
