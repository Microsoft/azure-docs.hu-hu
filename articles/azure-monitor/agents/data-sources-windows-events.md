---
title: Windows Eseménynapló-adatforrások összegyűjtése Log Analytics ügynökkel Azure Monitor
description: Ismerteti, hogyan konfigurálható a Windows-eseménynaplók gyűjteménye Azure Monitor és a létrehozott rekordok részleteivel.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: c911b3a2ef927bf000513665dfafd735e3bcf181
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100615535"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Windows-Eseménynapló adatforrásainak összegyűjtése Log Analytics ügynökkel
A Windows-eseménynaplók az egyik leggyakoribb [adatforrások](../agents/agent-data-sources.md) a Windows rendszerű virtuális gépeken található log Analytics ügynökök számára, mivel számos alkalmazás a Windows-eseménynaplóba ír.  Az eseményeket összegyűjtheti a szabványos naplókból, például a rendszerből és az alkalmazásból is, és megadhatja a figyelni kívánt alkalmazások által létrehozott egyéni naplókat.

> [!IMPORTANT]
> Ez a cikk a Windows-események összegyűjtését ismerteti a [log Analytics ügynökkel](../platform/log-analytics-agent.md) , amely a Azure monitor által használt ügynökök egyike. Más ügynökök különböző adatokat gyűjtenek, és eltérően vannak konfigurálva. A rendelkezésre álló ügynökök és az összegyűjtött adatok listáját lásd: [Azure monitor ügynökök áttekintése](../agents/agents-overview.md) .

![Windows-események](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Windows-eseménynaplók konfigurálása
Konfigurálja a Windows-eseménynaplókat az Log Analytics munkaterület [speciális beállításainak adatok menüjéből](../agents/agent-data-sources.md#configuring-data-sources) .

Azure Monitor csak a beállításokban megadott Windows-eseménynaplókban lévő eseményeket gyűjti.  Eseménynapló hozzáadásához írja be a napló nevét, majd kattintson a elemre **+** .  Minden napló esetében csak a kijelölt megszakításokkal rendelkező események lesznek összegyűjtve.  Tekintse át a gyűjteni kívánt naplóhoz tartozó megszakításokat.  Az események szűréséhez nem adhat meg további feltételeket.

Amikor beírja az Eseménynapló nevét, Azure Monitor a gyakori Eseménynapló-nevekre vonatkozó javaslatokat is tartalmaz. Ha a hozzáadni kívánt napló nem jelenik meg a listában, akkor a napló teljes nevének beírásával továbbra is hozzáadhatja azt. A napló teljes nevét az Eseménynapló használatával találja. Az eseménynaplóban nyissa meg a napló *Tulajdonságok* lapját, és másolja a karakterláncot a *teljes név* mezőből.

![Windows-események konfigurálása](media/data-sources-windows-events/configure.png)

> [!NOTE]
> A Windows-Eseménynapló kritikus eseményeinek súlyossága "Error" lesz a Azure Monitor naplókban.

## <a name="data-collection"></a>Adatgyűjtés
Azure Monitor gyűjt minden olyan eseményt, amely megfelel egy figyelt eseménynaplóból származó kiválasztott súlyosságnak, mivel az esemény létrejön.  Az ügynök rögzíti a helyét minden olyan eseménynaplóban, amelyről gyűjt.  Ha az ügynök egy ideig offline állapotba kerül, akkor akkor is gyűjti az eseményeket, amikor az ügynök offline állapotba került, még akkor is, ha ezek az események létre lettek hozva.  Ezek az események nem gyűjthetők össze, ha az Eseménynapló az ügynök offline állapotában felülírt, nem gyűjtött eseményekkel van becsomagolva.

>[!NOTE]
>A Azure Monitor nem gyűjti az SQL Server által létrehozott naplózási *eseményeket a 18453* -as azonosítójú azonosítójú eseménnyel, amely kulcsszavakat tartalmaz – a *klasszikus* vagy a *naplózási sikerességet* és a kulcsszavak *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Windows-események rekordjainak tulajdonságai
A Windows-események rekordjainak típusa **esemény** , és a tulajdonságok a következő táblázatban láthatók:

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Annak a számítógépnek a neve, amelyre az eseményt gyűjtötték. |
| EventCategory |Az esemény kategóriája. |
| EventData |Az összes eseményvezérelt érték nyers formátumban. |
| Eseményazonosító |Az esemény száma. |
| EventLevel |Az esemény súlyossága numerikus formában. |
| EventLevelName |Az esemény súlyossága szöveges formában. |
| EventLog |Annak az Eseménynaplónak a neve, amelyből az eseményt gyűjtötték. |
| ParameterXml |Az esemény paramétereinek értékei XML formátumban. |
| ManagementGroupName |System Center Operations Manager ügynökök felügyeleti csoportjának neve.  Más ügynökök esetében ez az érték `AOI-<workspace ID>` |
| RenderedDescription |Esemény leírása paraméter-értékekkel |
| Forrás |Az esemény forrása. |
| SourceSystem |Az a típusú ügynök, amelyből az eseményt gyűjtötték. <br> OpsManager – Windows-ügynök, közvetlen kapcsolat vagy Operations Manager felügyelt <br> Linux – minden Linux-ügynök  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Az esemény Windowsban történő létrehozásának dátuma és időpontja. |
| Felhasználónév |Az eseményt naplózó fiók felhasználóneve. |

## <a name="log-queries-with-windows-events"></a>Lekérdezések naplózása Windows-eseményekkel
Az alábbi táblázat a Windows-események rekordjait lekérő lekérdezések különböző példáit ismerteti.

| Lekérdezés | Description |
|:---|:---|
| Esemény |Minden Windows-esemény. |
| Az Event &#124;, ahol a EventLevelName = = "Error" |Minden Windows-esemény, amelynek súlyossága a hiba. |
| Event &#124; összegzések száma () forrás szerint |Windows-események száma forrás szerint. |
| Event &#124;, ahol a EventLevelName = = "Error" &#124; összesítések száma () forrás szerint |Windows-hibák száma forrás szerint. |


## <a name="next-steps"></a>Következő lépések
* A Log Analytics konfigurálásával más [adatforrásokat](../agents/agent-data-sources.md) is gyűjthet az elemzéshez.
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) .  
* [Teljesítményszámlálók gyűjteményének](data-sources-performance-counters.md) konfigurálása a Windows-ügynökökből.
