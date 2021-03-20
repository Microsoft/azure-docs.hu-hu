---
title: A SOC jobb kezelése az incidens metrikákkal az Azure Sentinelben | Microsoft Docs
description: Az Azure Sentinel incidens metrikák képernyőjéről és a munkafüzetből származó információk segítségével kezelheti a biztonsági operatív központot (SOC).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 408913fed864ee5f966b96c81afbfee4b2dc8678
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660729"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>A SOC hatékonyabb kezelése incidensmetrikákkal

> [!IMPORTANT]
> Az incidens metrikái szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Ezek a funkciók szolgáltatói szerződés nélkül vannak megadva, és nem ajánlottak éles számítási feladatokhoz.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Security Operations Center (SOC) kezelője számára a csapat teljesítményének méréséhez átfogó hatékonysági mérőszámokkal és mértékekkel kell rendelkeznie. Az incidensek műveleteinek időbeli alakulását számos különböző feltétel, például a súlyosság, a MITRE Tactics, az osztályozási idő, az átlagos megoldási idő és más feltételek alapján érdemes megtekinteni. Az Azure Sentinel mostantól elérhetővé teszi ezeket az adatokkal a Log Analytics új **biztonsági incidens** -tábláját és sémáját, valamint a kapcsolódó **biztonsági műveletek hatékonyságát** tartalmazó munkafüzetet. A csapat teljesítményét az idő múlásával jelenítheti meg, és ezzel a betekintéssel növelheti a hatékonyságot. Saját KQL-lekérdezéseket is írhat és használhat az incidensek táblájára, hogy testreszabott munkafüzeteket hozzon létre, amelyek megfelelnek az adott naplózási igényeknek és KPI-k számára.

## <a name="use-the-security-incidents-table"></a>A biztonsági incidensek tábla használata

A **biztonsági incidens** tábla az Azure sentinelbe van építve. A **naplókban** a **SecurityInsights** gyűjtemény többi táblájával is megtalálhatja. A Log Analytics bármely más táblájához hasonlóan lekérdezhető.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="Biztonsági incidensek táblázata":::

Minden alkalommal, amikor létrehoz vagy frissít egy incidenst, új naplóbejegyzés kerül a táblába. Így nyomon követheti az incidensek módosításait, és lehetővé teszi a még nagyobb teljesítményű SOC-metrikák használatát, de ennek szem előtt kell lennie, amikor lekérdezéseket készít ehhez a táblához, mivel előfordulhat, hogy el kell távolítania egy incidens ismétlődő bejegyzéseit (a futtatott pontos lekérdezéstől függően). 

Ha például vissza szeretné adni az incidensek száma szerint rendezett összes incidens listáját, de csak az incidensek legutóbbi naplóját szeretné visszaadni, ezt a KQL [Összefoglaló operátor](/azure/data-explorer/kusto/query/summarizeoperator) használatával teheti meg az `arg_max()` [összesítési függvénnyel](/azure/data-explorer/kusto/query/arg-max-aggfunction):


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>További példák a lekérdezésekre

Bezárási idő:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

Osztályozási idő átlaga:
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>Biztonsági műveletek hatékonyságát tartalmazó munkafüzet

A **SecurityIncidents** táblázat kiegészítéséhez egy beépített **biztonsági műveletek hatékonyságát** biztosító munkafüzet-sablont adtunk meg, amely a SoC-műveletek figyelésére használható. A munkafüzet a következő metrikákat tartalmazza: 
- Az incidens az idő múlásával lett létrehozva 
- A besorolás, a súlyosság, a tulajdonos és az állapot zárásával létrehozott incidensek 
- Osztályozási idő átlaga 
- Bezárási idő 
- A súlyosság, a tulajdonos, az állapot, a termék és a taktikák időbeli alakulásával létrehozott incidensek 
- Százalékok osztályozásának ideje 
- A százalékos érték lezárásának ideje 
- Az osztályozás átlagos ideje a tulajdonos szerint 
- Legutóbbi tevékenységek 
- Legutóbbi záró besorolások  

Ez az új munkafüzet-sablon az Azure Sentinel navigációs menüjében a **munkafüzetek** lehetőség kiválasztásával, majd a **sablonok** lapon található. Válassza ki a **biztonsági műveletek hatékonyságát** a katalógusból, és kattintson a **mentett munkafüzet megtekintése** és a **sablon megtekintése** gombokra.

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="Biztonsági incidensek munkafüzet-gyűjteménye":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="Biztonsági incidensek munkafüzete kész":::

A sablon használatával saját igényeihez igazított egyéni munkafüzeteket hozhat létre.

## <a name="securityincidents-schema"></a>SecurityIncidents séma

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>Következő lépések

- Az Azure Sentinel megkezdéséhez szüksége lesz egy előfizetésre Microsoft Azure. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/free/).
- Ismerje meg, hogyan hozhatja be [adatait az Azure sentinelbe](quickstart-onboard.md), és hogyan tekintheti [meg az adatait és a lehetséges fenyegetéseket](quickstart-get-visibility.md).