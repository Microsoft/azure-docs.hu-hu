---
title: Adatgyűjtés konfigurálása a Azure Monitor-ügynökhöz (előzetes verzió)
description: Ismerteti, hogyan lehet adatgyűjtési szabályt létrehozni a virtuális gépekről az Azure Monitor ügynök használatával történő adatgyűjtéshez.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 2a91062a701ca1b07f47f381a04cdf06c57c5746
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721528"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Adatgyűjtés konfigurálása a Azure Monitor-ügynökhöz (előzetes verzió)

Az adatgyűjtési szabályok (DCR) a Azure Monitorba érkező és a küldendő adatforrásokat határozzák meg. Ez a cikk azt ismerteti, hogyan lehet adatgyűjtési szabályt létrehozni a virtuális gépekről az Azure Monitor ügynök használatával történő adatgyűjtéshez.

Az adatgyűjtési szabályok teljes leírását lásd: [adatgyűjtési szabályok a Azure monitorban (előzetes verzió)](data-collection-rule-overview.md).

> [!NOTE]
> Ez a cikk azt ismerteti, hogyan konfigurálhatja a virtuális gépeket a jelenleg előzetes verzióban elérhető Azure Monitor ügynökkel. Az általánosan elérhető ügynökök leírását, valamint az adatok gyűjtését az [Azure monitor ügynökök áttekintését](agents-overview.md) ismertető cikkben talál.

## <a name="data-collection-rule-associations"></a>Adatgyűjtési szabály társításai

A DCR virtuális gépre való alkalmazásához létre kell hoznia egy társítást a virtuális géphez. Előfordulhat, hogy egy virtuális gép több DCR is társítható, és a DCR-hez több virtuális gép is tartozhat. Ez lehetővé teszi, hogy Definiáljon egy adott követelménynek megfelelő DCR-készletet, és azokat csak azokra a virtuális gépekre alkalmazza, amelyekre érvényesek. 

Vegyünk például egy olyan környezetet, amely egy üzletági alkalmazást futtató virtuális gépeket és a SQL Servert futtató többi alkalmazást futtat. Lehet, hogy egy alapértelmezett adatgyűjtési szabály vonatkozik az összes virtuális gépre, és elkülöníti az adatgyűjtési szabályokat, amelyek kifejezetten az üzletági alkalmazásra és a SQL Serverre vonatkozó adatokat gyűjtenek. Az adatgyűjtési szabályokhoz tartozó virtuális gépek társításai az alábbi ábrához hasonlóan jelennek meg.

![A diagramon az üzletági alkalmazások és a Central-i t-default és a LOB-alkalmazás nevű adatgyűjtési szabályokhoz társított virtuális SQL Server gépek jelennek meg a SQL Serverhoz.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Szabály és társítás létrehozása Azure Portalban

A Azure Portal használatával létrehozhat egy adatgyűjtési szabályt, és az előfizetésében lévő virtuális gépeket hozzárendelheti a szabályhoz. A rendszer automatikusan telepíti a Azure Monitor ügynököt, és felügyelt identitást hozott létre minden olyan virtuális géphez, amelyen még nincs telepítve.

> [!IMPORTANT]
> Jelenleg ismert probléma van, ha az adatgyűjtési szabály olyan virtuális gépen hoz létre felügyelt identitást, amely már rendelkezik felhasználó által hozzárendelt felügyelt identitással, a felhasználó által hozzárendelt identitás le van tiltva.

A Azure Portal **Azure monitor** menüjében válassza az **adatgyűjtési szabályok** lehetőséget a **Beállítások** szakaszban. Új adatgyűjtési szabály és hozzárendelés hozzáadásához kattintson a **Hozzáadás** gombra.

[![Adatgyűjtés szabályai](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Új szabály és társítások létrehozásához kattintson a **Hozzáadás** gombra. Adja meg a **szabály nevét** , és adja meg az **előfizetést** és az **erőforráscsoportot**. Itt adható meg, hogy a rendszer hová hozza létre a DCR-t. A virtuális gépek és azok társításai a bérlő bármelyik előfizetésében vagy erőforráscsoporthoz tartozhatnak.

[![Az adatgyűjtési szabály alapjai](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

A **Virtual Machines (virtuális gépek** ) lapon adja hozzá azokat a virtuális gépeket, amelyeknek alkalmaznia kell az adatgyűjtési szabályt. Az Azure Virtual Machines és az Azure arc-kompatibilis kiszolgálók is szerepelni fognak a környezetben. A Azure Monitor ügynök a még nem telepített virtuális gépekre lesz telepítve.

[![Adatgyűjtési szabály virtuális gépei](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

Az adatgyűjtés **és** küldés lapon kattintson az **adatforrás hozzáadása** lehetőségre egy adatforrás és egy célhely hozzáadásához. Válasszon ki egy **adatforrást**, és válassza ki a megfelelő adatokat a kiválasztáshoz. A teljesítményszámlálók esetében kiválaszthatja az objektumok előre meghatározott készletét és mintavételi sebességét. Eseményeknél naplókat vagy létesítményeket, valamint a súlyossági szintet is kiválaszthatja. 

[![Alapszintű adatforrás](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


Ha más naplókat és teljesítményszámlálókat szeretne megadni a [jelenleg támogatott adatforrásokból](azure-monitor-agent-overview.md#data-sources-and-destinations) , vagy az eseményeket XPath-lekérdezések használatával szeretné szűrni, válassza az **Egyéni** lehetőséget. Ezután megadhat egy [XPath ](https://www.w3schools.com/xml/xpath_syntax.asp) -értéket a gyűjtött értékek bármelyikéhez. Példák: példa a [DCR](data-collection-rule-overview.md#sample-data-collection-rule) -re.

[![Adatforrás egyéni](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

A **cél** lapon adjon hozzá egy vagy több célhelyet az adatforrás számára. A Windows-események és a syslog-adatforrások csak Azure Monitor naplókba küldhetők el. A teljesítményszámlálók mindkét Azure Monitor metrikára és Azure Monitor naplókra is küldhetnek.

[![Cél](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Kattintson az **adatforrás hozzáadása** lehetőségre, majd **tekintse át a + létrehozás** lehetőséget, és tekintse át az adatgyűjtési szabály és a virtuális gépek készletével való társítás részleteit. Kattintson a **Létrehozás** elemre a létrehozásához.

> [!NOTE]
> Az adatgyűjtési szabály és a társítások létrehozása után akár 5 percet is igénybe vehet, amíg a célhelyek el nem jutnak.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Az adatgyűjtés korlátozása egyéni XPath-lekérdezésekkel
Mivel egy Log Analytics munkaterületen összegyűjtött adatokért kell fizetnie, csak a szükséges adatokat kell összegyűjtenie. Az alapszintű konfiguráció használata a Azure Portalban csak korlátozott számú eseményt tud gyűjteni. Az alkalmazások és a rendszernaplók esetében ez az összes olyan napló, amely egy adott súlyossággal rendelkezik. Biztonsági naplók esetén ez az összes naplózási művelet, vagy az összes naplózási hiba naplója.

További szűrők megadásához egyéni konfigurációt kell használnia, és meg kell adnia egy XPath-t, amely kiszűri a nem használt eseményeket. Az XPath-bejegyzések az űrlapon íródnak `LogName!XPathQuery` . Előfordulhat például, hogy csak az alkalmazás eseménynaplójában lévő eseményeket szeretné visszaadni az 1035-es azonosítójú eseményhez. Az események XPathQuery `*[System[EventID=1035]]` . Mivel az alkalmazás eseménynaplójában szeretné lekérni az eseményeket, az XPath a következő lesz: `Application!*[System[EventID=1035]]`

Tekintse meg az [xpath 1,0 korlátozásait](/windows/win32/wes/consuming-events#xpath-10-limitations) a Windows-Eseménynapló által támogatott XPath-korlátozások listájában.

> [!TIP]
> A `Get-WinEvent` `FilterXPath` XPathQuery érvényességének teszteléséhez használja a PowerShell-parancsmagot a paraméterrel. Az alábbi parancsfájl egy példát mutat be.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Ha a rendszer visszaadja az eseményeket, a lekérdezés érvényes.
> - Ha a *rendszer nem talált olyan eseményt, amely megfelel a megadott kiválasztási feltételeknek*, a lekérdezés érvényes lehet, de a helyi gépen nincsenek egyező események.
> - Ha a *megadott lekérdezés érvénytelen* üzenetet kap, a lekérdezés szintaxisa érvénytelen. 

A következő táblázat példákat mutat be az események egyéni XPath használatával történő szűrésére.

| Description |  XPath |
|:---|:---|
| Csak a rendszeresemények összegyűjtése a következő azonosítójú eseménnyel: eseményazonosító = 4648 |  `System!*[System[EventID=4648]]`
| Csak olyan rendszeresemények gyűjtése, amelyeknek az eseményazonosító = 4648 és a consent.exe folyamat neve |  `System!*[System[(EventID=4648) and (EventData[@Name='ProcessName']='C:\Windows\System32\consent.exe')]]`
| Az összes kritikus, hiba, figyelmeztetés és információs esemény összegyűjtése a rendszer eseménynaplójában, kivéve az Event ID = 6 (az illesztőprogram betöltve) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Az összes sikeres és sikertelen biztonsági esemény gyűjtése a 4624-es azonosítójú esemény kivételével (sikeres bejelentkezés) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Szabály és társítás létrehozása REST API használatával

Az alábbi lépéseket követve hozzon létre egy adatgyűjtési szabályt és társításokat a REST API használatával.

1. Manuálisan hozza létre a DCR-fájlt a [DCR](data-collection-rule-overview.md#sample-data-collection-rule)-ben látható JSON-formátum használatával.

2. Hozza létre a szabályt a [REST API](/rest/api/monitor/datacollectionrules/create#examples)használatával.

3. Hozzon létre egy társítást az egyes virtuális gépekhez az adatgyűjtési szabályhoz a [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples)használatával.


## <a name="create-association-using-resource-manager-template"></a>Társítás létrehozása a Resource Manager-sablonnal

Resource Manager-sablonnal nem hozhatók létre adatgyűjtési szabályok, de az Azure-beli virtuális gépek vagy az Azure arc-kompatibilis kiszolgálók között egy Resource Manager-sablonnal is létrehozhatók társítások. A példákat lásd: [Resource Manager-sablonok minták a Azure monitor adatgyűjtési szabályaihoz](./resource-manager-data-collection-rules.md) a sablonokhoz.



## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor-ügynökről](azure-monitor-agent-overview.md).
- További információ az [adatgyűjtési szabályokról](data-collection-rule-overview.md).
