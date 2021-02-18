---
title: Adatgyűjtés konfigurálása a Azure Monitor-ügynökhöz (előzetes verzió)
description: Ismerteti, hogyan lehet adatgyűjtési szabályt létrehozni a virtuális gépekről az Azure Monitor ügynök használatával történő adatgyűjtéshez.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 6e65b828e2cf86614707c8a859abc0c4f2395899
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616365"
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


## <a name="create-rule-and-association-using-rest-api"></a>Szabály és társítás létrehozása REST API használatával

Az alábbi lépéseket követve hozzon létre egy adatgyűjtési szabályt és társításokat a REST API használatával.

1. Manuálisan hozza létre a DCR-fájlt a [DCR](data-collection-rule-overview.md#sample-data-collection-rule)-ben látható JSON-formátum használatával.

2. Hozza létre a szabályt a [REST API](/rest/api/monitor/datacollectionrules/create#examples)használatával.

3. Hozzon létre egy társítást az egyes virtuális gépekhez az adatgyűjtési szabályhoz a [REST API](/rest/api/monitor/datacollectionruleassociations/create#examples)használatával.


## <a name="create-association-using-resource-manager-template"></a>Társítás létrehozása a Resource Manager-sablonnal

Resource Manager-sablonnal nem hozhatók létre adatgyűjtési szabályok, de az Azure-beli virtuális gépek vagy az Azure arc-kompatibilis kiszolgálók között egy Resource Manager-sablonnal is létrehozhatók társítások. A példákat lásd: [Resource Manager-sablonok minták a Azure monitor adatgyűjtési szabályaihoz](../samples/resource-manager-data-collection-rules.md) a sablonokhoz.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor-ügynökről](azure-monitor-agent-overview.md).
- További információ az [adatgyűjtési szabályokról](data-collection-rule-overview.md).
