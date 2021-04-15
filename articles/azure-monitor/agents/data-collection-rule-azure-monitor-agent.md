---
title: Adatgyűjtés konfigurálása a Azure Monitor ügynökhöz (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan hozható létre adatgyűjtési szabály a virtuális gépekről a virtuális gépekről a Azure Monitor használatával.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.openlocfilehash: 884f048ac099cfc6b799fe266172a0eecef3db6f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478369"
---
# <a name="configure-data-collection-for-the-azure-monitor-agent-preview"></a>Adatgyűjtés konfigurálása a Azure Monitor ügynökhöz (előzetes verzió)

Az adatgyűjtési szabályok (Data Collection Rules, DCR) határozzák meg a Azure Monitor és megszabadják az adatok elküldési helyének megadását. Ez a cikk bemutatja, hogyan hozhat létre olyan adatgyűjtési szabályt, amely adatokat gyűjt a virtuális gépekről a Azure Monitor ügynök használatával.

Az adatgyűjtési szabályok teljes leírását lásd: Adatgyűjtési szabályok a [Azure Monitor (előzetes verzió) .](data-collection-rule-overview.md)

> [!NOTE]
> Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek adatait a jelenleg előzetes verzióban Azure Monitor ügynökkel. Az [általánosan Azure Monitor](agents-overview.md) ügynökök leírását és az adatok gyűjtésére való használatuk ismertetését lásd: Overview of Azure Monitor agents (A Azure Monitor-ügynökök áttekintése).

## <a name="data-collection-rule-associations"></a>Adatgyűjtési szabályok társításai

A DCR virtuális gépre való alkalmazáshoz létre kell hoznia egy társítást a virtuális géphez. Egy virtuális gép több tartományvezérlőhöz is társítva lehet, és egy DCR-hez több virtuális gép is társítva lehet. Ez lehetővé teszi tartományvezérlők egy adott követelménynek megfelelő készletének definiálát, és csak azokra a virtuális gépekre alkalmazza őket, amelyekre érvényesek. 

Vehet például egy olyan környezetet, amely egy üzletági alkalmazást futtató virtuális gépeket, mások pedig SQL Server. Előfordulhat, hogy van egy alapértelmezett adatgyűjtési szabálya, amely az összes virtuális gépre vonatkozik, és külön adatgyűjtési szabályokkal, amelyek kifejezetten az üzleti alkalmazásra és a virtuális gépekre vonatkozó adatokat SQL Server. A virtuális gépek és az adatgyűjtési szabályok társításai az alábbi ábrához hasonlóan néznek ki.

![Diagram az üzletági alkalmazásokat üzemeltető virtuális gépeket és SQL Server központi-i t-default nevű adatgyűjtési szabályokhoz és üzletági alkalmazásokhoz, valamint a központi-i t-default és az s q l-hez társított SQL Server.](media/data-collection-rule-azure-monitor-agent/associations.png)



## <a name="create-rule-and-association-in-azure-portal"></a>Szabály és társítás létrehozása a Azure Portal

A virtuális Azure Portal létrehozhat egy adatgyűjtési szabályt, és hozzárendelheti az előfizetésében lévő virtuális gépeket a szabályhoz. A Azure Monitor ügynök automatikusan telepítve lesz, és létrejön egy felügyelt identitás minden olyan virtuális géphez, amely még nincs telepítve.

> [!IMPORTANT]
> Jelenleg van egy ismert probléma, amely miatt, ha az adatgyűjtési szabály létrehoz egy felügyelt identitást egy olyan virtuális gépen, amely már rendelkezik felhasználó által hozzárendelt felügyelt identitással, a felhasználó által hozzárendelt identitás le lesz tiltva.

A **Azure Monitor** menüben válassza Azure Portal **Adatgyűjtési** szabályok lehetőséget a **Beállítások szakaszban.** Új **adatgyűjtési** szabály és hozzárendelés hozzáadásához kattintson a Hozzáadás gombra.

[![Adatgyűjtési szabályok](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rules.png#lightbox)

Kattintson **a Hozzáadás** gombra egy új szabály és társításhalmaz létrehozásához. Adjon meg egy **szabálynevet,** és adjon meg egy **előfizetést** és **egy erőforráscsoportot.** Ez határozza meg a DCR létrehozási helyét. A virtuális gépek és társításaik a bérlő bármely előfizetésében vagy erőforráscsoportában lehetek.

[![Adatgyűjtési szabályok – alapok](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-basics.png#lightbox)

A Virtuális **gépek lapon** adjon hozzá olyan virtuális gépeket, amelyekre alkalmazni kell az adatgyűjtési szabályt. Mind az Azure-beli virtuális Azure Arc, mind a környezetben engedélyezett kiszolgálóknak fel kell sorolva lennie. A Azure Monitor Agent olyan virtuális gépekre lesz telepítve, amelyeken még nincs telepítve.

[![Adatgyűjtési szabály virtuális gépei](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-virtual-machines.png#lightbox)

A Collect **and deliver (Gyűjtés és kézbesítés) lapon** kattintson az Add data source **(Adatforrás hozzáadása)** elemre egy adatforrás- és célkészlet hozzáadásához. Válassza ki az **Adatforrás típusát,** és megjelennek a kiválasztható adatok. A teljesítményszámlálókhoz előre meghatározott objektumkészletből és azok mintavételezési arányból választhat. Eseményekhez választhat a naplók vagy létesítmények egy halmaza és a súlyossági szint közül. 

[![Adatforrás alapszintű](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-basic.png#lightbox)


A jelenleg támogatott adatforrások egyéb [](azure-monitor-agent-overview.md#data-sources-and-destinations) naplóinak és teljesítményszámlálóinak megadásához vagy az események XPath-lekérdezésekkel való szűréséhez válassza az Egyéni **lehetőséget.** Ezután megadhat egy [XPath-t ](https://www.w3schools.com/xml/xpath_syntax.asp) a begyűjteni kívánt értékekhez. Példákért tekintse meg a [Minta DCR-t.](data-collection-rule-overview.md#sample-data-collection-rule)

[![Adatforrás egyéni](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-data-source-custom.png#lightbox)

A Cél **lapon** adjon hozzá egy vagy több célhelyet az adatforráshoz. A Windows-esemény- és Syslog-adatforrások csak a naplókba Azure Monitor küldenek. A teljesítményszámlálók a metrikáknak és Azure Monitor naplóknak Azure Monitor küldenek.

[![Cél](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png)](media/data-collection-rule-azure-monitor-agent/data-collection-rule-destination.png#lightbox)

Kattintson **az Adatforrás hozzáadása,** majd a Felülvizsgálat **+ létrehozás** elemre az adatgyűjtési szabály részleteinek és a virtuális gépek készletéhez való társításának áttekintéséhez. Kattintson **a Létrehozás** gombra a létrehozásához.

> [!NOTE]
> Az adatgyűjtési szabály és a társítások létrehozása után akár 5 percig is eltarthat, mire a rendszer elküldi az adatokat a célhelyre.

## <a name="limit-data-collection-with-custom-xpath-queries"></a>Adatgyűjtés korlátozása egyéni XPath-lekérdezésekkel
Mivel a Log Analytics-munkaterületen gyűjtött adatokért díjat kell fizetnie, csak a szükséges adatokat kell összegyűjtenie. Ha alapszintű konfigurációt használ a Azure Portal csak korlátozottan tudja szűrni a gyűjthető eseményeket. Alkalmazás- és rendszernaplókhoz ez mind egy adott súlyosságú napló. Biztonsági naplók esetén ez mind a sikeres naplózási vagy az audithiba-naplókra vonatkozó.

További szűrők megadásához egyéni konfigurációt kell használnia, és meg kell adnia egy XPath-t, amely kiszűri a nem használt eseményeket. Az XPath-bejegyzések a következő formában vannak `LogName!XPathQuery` megírva: . Előfordulhat például, hogy csak az 1035-ös eseményazonosítójú eseményeket szeretné visszaadni az alkalmazás eseménynaplójában. Ezen események XPathQuery-lekérdezése a következő lenne: `*[System[EventID=1035]]` . Mivel az eseményeket az alkalmazás eseménynaplójában szeretné lekérni, az XPath a következő lenne: `Application!*[System[EventID=1035]]`

A Windows eseménynapló által támogatott XPath korlátozási listájáért tekintse meg az XPath [1.0](/windows/win32/wes/consuming-events#xpath-10-limitations) korlátozásai részt.

> [!TIP]
> Az XPathQuery érvényességének tesztelésére használja a PowerShell-parancsmagot `Get-WinEvent` `FilterXPath` a paraméterrel. Az alábbi szkript erre mutat egy példát.
> 
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Ha a visszaadott események érvényesek, a lekérdezés érvényes.
> - Ha a Nem találhatók olyan események, amelyek megfelelnek a megadott kiválasztási feltételeknek üzenet jelenik *meg,* a lekérdezés érvényes lehet, de nincsenek egyező események a helyi gépen.
> - Ha a Következő üzenet jelenik meg: A megadott lekérdezés *érvénytelen,* a lekérdezés szintaxisa érvénytelen. 

Az alábbi táblázat példákat mutat be az események egyéni XPath használatával való szűrésére.

| Leírás |  Xpath |
|:---|:---|
| Csak olyan rendszeresemények gyűjtése, amelyek eseményazonosítója = 4648 |  `System!*[System[EventID=4648]]`
| Csak olyan rendszereseményeket gyűjtsön, amelyek eseményazonosítója = 4648, és a folyamat neve consent.exe | `Security!*[System[(EventID=4648)]] and *[EventData[Data[@Name='ProcessName']='C:\Windows\System32\consent.exe']]` |
| Gyűjtsön össze minden kritikus, hiba-, figyelmeztetési és információs eseményt a rendszer eseménynaplójában, kivéve az Eseményazonosító = 6 (illesztőprogram betöltve) |  `System!*[System[(Level=1 or Level=2 or Level=3) and (EventID != 6)]]` |
| Gyűjtsön össze minden sikeres és sikertelen biztonsági eseményt, kivéve a 4624-es eseményazonosítót (sikeres bejelentkezés) |  `Security!*[System[(band(Keywords,13510798882111488)) and (EventID != 4624)]]` |


## <a name="create-rule-and-association-using-rest-api"></a>Szabály és társítás létrehozása REST API

Az alábbi lépésekkel hozzon létre egy adatgyűjtési szabályt és társításokat a REST API.

1. Hozza létre manuálisan a DCR-fájlt a minta [DCR-fájlban látható JSON-formátummal.](data-collection-rule-overview.md#sample-data-collection-rule)

2. Hozza létre a szabályt a [REST API.](/rest/api/monitor/datacollectionrules/create#examples)

3. Hozzon létre társítást az egyes virtuális gépek és az adatgyűjtési szabályok között az [REST API.](/rest/api/monitor/datacollectionruleassociations/create#examples)


## <a name="create-association-using-resource-manager-template"></a>Társítás létrehozása Resource Manager sablonnal

Egy Azure-beli virtuális gép vagy egy engedélyezett Azure Arc között létrehozhat társítást egy Resource Manager sablon használatával. A [Resource Manager a sablonmintákat](./resource-manager-data-collection-rules.md) az adatgyűjtési szabályokhoz Azure Monitor sablonmintákat tartalmazó dokumentumban.



## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Monitor ügynökről.](azure-monitor-agent-overview.md)
- További információ az [adatgyűjtési szabályokról.](data-collection-rule-overview.md)
