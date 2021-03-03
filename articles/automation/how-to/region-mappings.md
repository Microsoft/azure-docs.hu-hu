---
title: A társított Log Analytics-munkaterület esetében támogatott régiók
description: Ez a cikk az Automation-fiók és a Log Analytics munkaterület közötti támogatott régió-hozzárendeléseket ismerteti, mivel azok a Azure Automation egyes szolgáltatásaihoz kapcsolódnak.
ms.date: 02/17/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 0599dcb57b46d1e48b4035acac8b64edbbe06912
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720171"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>A társított Log Analytics-munkaterület esetében támogatott régiók

Azure Automation a kiszolgálók és a virtuális gépek Update Management, Change Tracking és leltár, valamint Start/Stop VMs during off-hours funkcióit engedélyezheti. Ezek a funkciók egy Log Analytics munkaterülettől függenek, ezért a munkaterület Automation-fiókkal való összekapcsolását igényli. Azonban csak bizonyos régiók támogatottak egymás összekapcsolásához. Általánosságban elmondható, hogy a leképezés *nem* alkalmazható, ha olyan munkaterülethez szeretne Automation-fiókot kapcsolni, amely nem engedélyezi ezeket a funkciókat.

Az itt tárgyalt leképezések csak az Log Analytics munkaterület Automation-fiókhoz való csatolására vonatkoznak. Nem vonatkoznak azokra a virtuális gépekre (VM-EK), amelyek az Automation-fiókhoz kapcsolt munkaterülethez csatlakoznak. A virtuális gépek nem korlátozódnak az adott Log Analytics munkaterület által támogatott régiókra. Bármelyik régióban lehet. Ne feledje, hogy a virtuális gépek egy másik régióban is befolyásolhatják az állami, a helyi és az országokra vonatkozó szabályozási követelményeket, vagy a vállalat megfelelőségi követelményeit. Egy másik régióban lévő virtuális gépek is bevezethetik az adatsávszélességi díjakat.

Mielőtt a virtuális gépeket egy másik régióban lévő munkaterülethez csatlakoztatja, tekintse át a követelményeket és a lehetséges költségeket a jogi és a költségek következményeinek megerősítéséhez és megismeréséhez.

Ez a cikk a támogatott leképezéseket ismerteti az Automation-fiókban lévő funkciók sikeres engedélyezéséhez és használatához.

További információ: [log Analytics munkaterület és Automation-fiók](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Támogatott leképezések

> [!NOTE]
> Ahogy az a következő táblázatban is látható, csak egy leképezés létezhet a Log Analytics és Azure Automation között.

A következő táblázat a támogatott leképezéseket tartalmazza:

|**Log Analytics munkaterület-régió**|**Azure Automation régió**|
|---|---|
|**USA**||
|<sup>1</sup> . EastUS|EastUS2|
|<sup>2</sup> . EastUS2|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|WestCentralUS|WestCentralUS|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Ázsia és a Csendes-óceáni térség**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|EastAsia|EastAsia|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|<sup>3</sup> . ChinaEast2|ChinaEast2|
|JapanEast|JapanEast|
|**Európa**||
|NorthEurope|NorthEurope|
|FranceCentral|FranceCentral|
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|SwitzerlandNorth|SwitzerlandNorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|<sup>3</sup> . USGovArizona|USGovArizona|



<sup>1</sup> a log Analytics-munkaterületek Automation-fiókokba való EastUS nem pontos a régiók közötti leképezés, de a megfelelő leképezés.

<sup>2</sup> a log Analytics-munkaterületek Automation-fiókokba való EastUS2 nem a régió – régió típusú leképezés, hanem a megfelelő leképezés.

<sup>3</sup> ebben a régióban csak Update Management támogatott, és más funkciók, például a Change Tracking és a leltár jelenleg nem érhetők el.

## <a name="unlink-a-workspace"></a>Munkaterület leválasztása

Ha úgy dönt, hogy már nem szeretné integrálni az Automation-fiókot egy Log Analytics munkaterülettel, közvetlenül a Azure Portalból is leválaszthatja a fiókját. A továbblépés előtt először [el kell távolítania](move-account.md#remove-features) Update Management, Change Tracking és leltárt, és Start/Stop VMS During off-hours, ha használja őket. Ha nem távolítja el őket, nem fejezheti be a leválasztási műveletet.

A funkciók eltávolítva az Automation-fiók összekapcsolásának megszüntetéséhez kövesse az alábbi lépéseket.

> [!NOTE]
> Előfordulhat, hogy egyes funkciók, például az Azure SQL monitoring megoldás korábbi verzióiban olyan Automation-eszközöket hoztak létre, amelyeket el kell távolítani a munkaterület leválasztása előtt.

1. Nyissa meg az Automation-fiókját a Azure Portal. Az Automation-fiók lapon a **kapcsolódó erőforrások** területen válassza a **csatolt munkaterület** lehetőséget.

2. A munkaterület megszüntetése lapon válassza a **munkaterület** leválasztása lehetőséget. Ha folytatni kívánja a folytatást, a rendszer rákérdez az ellenőrzésre.

3. Habár Azure Automation a fiók leválasztása a Log Analytics munkaterületről, a menü **értesítések** részén nyomon követheti a folyamat állapotát.

4. Ha Update Management használ, szükség esetén előfordulhat, hogy el szeretné távolítani a már nem szükséges alábbi elemeket:

    * Frissítési ütemtervek: mindegyiknek van egy olyan neve, amely megfelel egy Ön által létrehozott frissítés-telepítésnek.
    * A szolgáltatáshoz létrehozott hibrid feldolgozói csoportok: mindegyikhez hasonló név tartozik  `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8` .

5. Ha Start/Stop VMs during off-hours használ, opcionálisan eltávolíthatja a következő elemeket, amelyekre már nincs szükség:

    * VM runbook-ütemtervek elindítása és leállítása
    * VM-runbookok elindítása és leállítása
    * Változók

Azt is megteheti, hogy leválasztja a munkaterületet az Automation-fiókjából a munkaterületen belül.

1. A munkaterületen válassza az **Automation-fiók** lehetőséget a **kapcsolódó erőforrások** területen.
2. Az Automation-fiók lapon válassza a **fiók megszüntetése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg Update Management [Update Management áttekintésében](../update-management/overview.md).
* Ismerkedjen meg a Change Tracking és a leltárral [change Tracking és leltár áttekintésében](../change-tracking/overview.md).
* Ismerkedjen meg Start/Stop VMs during off-hours [Start/Stop VMS During off-hours áttekintésében](../automation-solution-vm-management.md).