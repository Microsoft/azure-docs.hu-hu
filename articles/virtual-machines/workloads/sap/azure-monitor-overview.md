---
title: Az SAP-megoldások áttekintése és architektúrája Azure Monitor | Microsoft Docs
description: Ez a cikk az Azure monitor for SAP-megoldásokkal kapcsolatos gyakori kérdésekre ad választ.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 411a95154c9ca36595dff4472e9ab8e1ae8a767e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571365"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>SAP-megoldások Azure monitorja (előzetes verzió)

## <a name="overview"></a>Áttekintés

Az SAP-megoldások Azure Monitor az Azure-beli natív figyelési termék, amely az Azure-beli SAP-tájakat futtatja az ügyfelek számára. A termék az [azure Virtual Machines](./hana-get-started.md) és az [SAP Azure-beli nagyméretű példányokon](./hana-overview-architecture.md)is működik.
Az SAP-megoldások Azure Monitor segítségével az ügyfelek egy központi helyen gyűjthetik össze az Azure-infrastruktúrából és-adatbázisokból származó telemetria adatokat, és a gyorsabb hibaelhárítás érdekében vizuálisan korrelálják a telemetria adatokat.

Az SAP-megoldások Azure Monitor az Azure Marketplace-en keresztül érhető el. Egyszerű, intuitív telepítési élményt nyújt, és mindössze néhány kattintással üzembe helyezi az erőforrást Azure Monitor SAP-megoldások (más néven SAP- **figyelő erőforrás**) számára.

Az ügyfelek az SAP-környezet különböző összetevőit figyelheti, például az Azure Virtual Machines, a magas rendelkezésre állású fürtöt, a SAP HANA adatbázist stb.) az adott összetevő megfelelő **szolgáltatójának** hozzáadásával.

Támogatott infrastruktúra:

- Azure-beli virtuális gép
- Nagyméretű Azure-példány

Támogatott adatbázisok:
- SAP HANA-adatbázis
- Microsoft SQL Server

Az SAP-megoldások Azure Monitor a meglévő [Azure monitor](../../../azure-monitor/overview.md) képességek, például a log Analytics és a [munkafüzetek](../../../azure-monitor/visualize/workbooks-overview.md) hatékonyságát használják több figyelési képesség biztosításához. Az ügyfelek [Egyéni vizualizációkat](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) hozhatnak létre az SAP-megoldások Azure monitor által biztosított alapértelmezett munkafüzetek szerkesztésével, [Egyéni lekérdezések](../../../azure-monitor/logs/log-analytics-tutorial.md) írásához és [egyéni riasztások](../../../azure-monitor/alerts/tutorial-response.md) létrehozásához az Azure log Analytics-munkaterületen, kihasználva a [rugalmas megőrzési időt](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) , és összekapcsolhatók a megfigyelési adatokkal a jegyrendszer használatával.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Az SAP-megoldások Azure Monitor milyen adatokat gyűjtenek?

Az SAP-megoldások Azure Monitor az adatgyűjtés az ügyfelek által konfigurált szolgáltatóktól függ. A nyilvános előzetes verzió során a rendszer a következő adatokat gyűjti össze.

Magas rendelkezésre állású pacemaker-fürt telemetria:
- Csomópont-, erőforrás-és SBD-eszköz állapota
- Pacemaker-helyek korlátozásai
- Kvórum szavazatainak és a gyűrű állapota
- [Továbbiak](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetria:
- CPU, memória, lemez és hálózat kihasználtsága
- HANA rendszerreplikáció (HSR)
- HANA biztonsági mentés
- HANA-gazdagép állapota
- A kiszolgáló és a névkiszolgáló szerepköreinek indexelése

Microsoft SQL Server telemetria:
- PROCESSZOR, memória, lemez kihasználtsága
- Állomásnév, SQL-példány neve, SAP-rendszerazonosító
- A Batch-kérelmek,-fordítások és az oldal várható élettartama az idő függvényében
- Az első 10 legdrágább SQL-utasítás az idő múlásával
- Az SAP-rendszeren az első 12 legnagyobb táblázat
- A SQL Server-hibanapló által rögzített problémák
- Folyamatok és az SQL WAIT statisztikáinak időbeli blokkolása

Operációs rendszer telemetria (Linux) 
- CPU-kihasználtság, elágazások száma, futó és letiltott folyamatok. 
- Memória kihasználtsága és eloszlása a felhasznált, gyorsítótárazott, pufferelt elemek között. 
- Kicseréli a kihasználtságot, a lapozást és a swap-arányt. 
- Fájlrendszerek kihasználtsága, az olvasott és írt bájtok száma blokkos eszközökön. 
- Írási/olvasási késés egy blokkos eszközön. 
- Folyamatos I/O-darabszám, állandó memória olvasási/írási bájtjai. 
- Hálózati csomagok be/ki, hálózati bájtok be/ki 

## <a name="data-sharing-with-microsoft"></a>Adatmegosztás a Microsofttal

Az SAP-megoldások Azure Monitor a rendszermetaadatokat gyűjt az SAP Azure-ügyfeleken történő továbbfejlesztett támogatásának biztosításához. A rendszer nem gyűjt személyes adatokat vagy EUII.
Az ügyfelek az SAP-megoldások erőforrásának létrehozásakor lehetővé teszik az adatok megosztását a Microsofttal Azure Monitor a legördülő menüből a *megosztás* lehetőség kiválasztásával.
Erősen ajánlott az ügyfelek számára engedélyezni az adatmegosztást, mivel a Microsoft támogatási és mérnöki csapatainak további információkkal szolgálnak az ügyfél-környezetről, és továbbfejlesztett támogatást biztosítanak a kritikus fontosságú SAP Azure-ügyfelek számára.

## <a name="architecture-overview"></a>Az architektúra áttekintése

A következő ábra azt mutatja be, hogy az SAP-megoldások Azure Monitor hogyan gyűjti a telemetria-t SAP HANA-adatbázisból. Az architektúra független az Azure-Virtual Machines vagy az Azure-beli nagyméretű példányokon SAP HANA üzembe helyezésével.

![Azure Monitor az SAP Solutions architektúrához](./media/azure-monitor-sap/azure-monitor-architecture.png)

Az architektúra legfontosabb összetevői a következők:
- Azure Portal – a kiindulási pont az ügyfelek számára. Az ügyfelek a piactéren Azure Portal és az SAP-megoldások felderítése céljából is felfedezhetik Azure Monitor
- Azure Monitor SAP-megoldások erőforráshoz – az ügyfelek számára az a hely, ahol megtekintheti a figyelési telemetria
- Felügyelt erőforráscsoport – az SAP-megoldások erőforrás-telepítésének Azure Monitor részeként automatikusan települt. A felügyelt erőforráscsoport súgójában üzembe helyezett erőforrások a telemetria gyűjteményében találhatók. Az üzembe helyezett fő erőforrások és a céljuk a következők:
   - Azure-beli virtuális gép: *gyűjtő virtuális* gépnek is nevezzük. Ez egy Standard_B2ms virtuális gép. Ennek a virtuális gépnek a fő célja a *figyelési tartalom* üzemeltetése. A figyelési tartalom arra a logikára utal, amely a telemetria és a begyűjtött adatoknak a megfigyelési keretrendszerbe való továbbítására szolgál. A fenti ábrán a figyelési tartalom tartalmazza azt a logikát, amely az SQL-porton keresztül SAP HANA adatbázishoz csatlakozik.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): ezt az erőforrást úgy helyezi üzembe, hogy biztonságosan tárolja SAP HANA adatbázis hitelesítő adatait, és adatokat tároljon a [szolgáltatókról](./azure-monitor-providers.md).
   - Log Analytics munkaterület: az a cél, ahol a telemetria-adat található.
      - A vizualizáció az [Azure-munkafüzetek](../../../azure-monitor/visualize/workbooks-overview.md)használatával log Analytics telemetria épül. Az ügyfelek testre szabhatják a vizualizációt. Az ügyfelek az Azure irányítópulton is rögzíthetik a munkafüzeteket vagy adott vizualizációkat az Azure irányítópulton az autofrissítési képességhez, amely a legalacsonyabb részletességgel 30 percet vesz igénybe.
      - Az ügyfelek a meglévő munkaterületet az adott előfizetésen belül, az SAP-figyelő erőforrással is használhatják, ha ezt a lehetőséget választja az üzembe helyezés időpontjában.
      - A Kusto lekérdezési nyelv (KQL) segítségével az ügyfelek [lekérdezéseket](../../../azure-monitor/logs/log-query-overview.md) futtathatnak log Analytics munkaterületen belüli nyers táblákon. Tekintse meg az *Egyéni naplókat*.

> [!Note]
> Az ügyfelek feladata a felügyelt erőforráscsoport keretében üzembe helyezett virtuális gép javítása és karbantartása.

> [!Tip]
> Az ügyfelek választhatnak egy meglévő Log Analytics munkaterületet a telemetria-gyűjteményhez, ha ugyanazon Azure-előfizetésen belül vannak telepítve, mint az SAP-megoldások Azure Monitor erőforrása.

### <a name="architecture-highlights"></a>Architektúra-kiemelés

Az architektúra legfontosabb főbb jellemzői a következők:
 - **Több példány** – az ügyfelek létrehozhatnak figyelőt egy adott összetevő típusának (például HANA db, ha fürt, Microsoft SQL Server) több, a VNET belüli SAP-SID-n belül, egyetlen erőforrás-Azure monitor SAP-megoldásokhoz.
 - **Több szolgáltató** – a fenti architektúra diagram a SAP HANA szolgáltatót mutatja be példaként. Hasonlóképpen, az ügyfelek további szolgáltatókat is konfigurálnak a megfelelő összetevőkhöz (például HANA DB, HA cluster, Microsoft SQL Server) az ezekből az összetevőkből származó adatok gyűjtéséhez.
 - **Nyílt forráskód** – az SAP-megoldások Azure monitor forráskódja elérhető a [githubon](https://github.com/Azure/AzureMonitorForSAPSolutions). Az ügyfelek hivatkozhatnak a szolgáltatói kódra, és további információt kaphatnak a termékről, illetve a visszajelzések megosztásáról vagy megosztásáról.
 - **Bővíthető lekérdezési keretrendszer** – a telemetria-adatok gyűjtésére szolgáló SQL-lekérdezések [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json)-ban íródnak. A további telemetria adatok gyűjtéséhez több SQL-lekérdezés is könnyen hozzáadható. Az ügyfelek a jelen dokumentum végén található hivatkozáson keresztül, illetve a felhasználói fiókkal való kapcsolatfelvétel során kérhetik az SAP-megoldások Azure Monitorához adott telemetria-adatmennyiséget.

## <a name="pricing"></a>Díjszabás
Az SAP-megoldások Azure Monitor egy ingyenes termék (nincs licenc díj). Az ügyfelek feladata, hogy kifizessék a felügyelt erőforráscsoport mögöttes összetevőinek költségeit.

## <a name="next-steps"></a>Következő lépések

Ismerje meg a szolgáltatókat, és hozza létre az első Azure Monitor az SAP Solutions-erőforráshoz.
 - További információ a [szolgáltatókról](./azure-monitor-providers.md)
 - [SAP-megoldások Azure Monitor üzembe helyezése Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Kérdése van az SAP-megoldások Azure Monitorával kapcsolatban? Olvassa el a [Gyakori kérdések](./azure-monitor-faq.md) szakaszt.
