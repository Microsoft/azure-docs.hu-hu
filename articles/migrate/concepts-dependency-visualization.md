---
title: Függőségek elemzése Azure Migrate felderítés és értékelés során
description: Ismerteti, hogyan használható a függőségi elemzés az értékeléshez Azure Migrate felderítés és értékelés használatával.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 03/18/2021
ms.openlocfilehash: 184c8099c0e86d8f8744948137b344c732bbf7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778372"
---
# <a name="dependency-analysis"></a>Függőségek elemzése

Ez a cikk a függőségek elemzését ismerteti Azure Migrateban: felderítés és Értékelés.

A függőségi elemzés azonosítja a felderített helyszíni kiszolgálók közötti függőségeket. A következő előnyöket biztosítja:

- A kiszolgálók csoportokba gyűjthetők felmérésre, pontosabban, nagyobb megbízhatósággal.
- Megadhatja azokat a kiszolgálókat, amelyeknek együtt kell áttelepítenie. Ez különösen akkor hasznos, ha nem biztos abban, hogy mely kiszolgálók tartoznak az Azure-ba migrálni kívánt alkalmazás-telepítéshez.
- Megadhatja, hogy a kiszolgálók használatban vannak-e, és hogy az áttelepítés helyett mely kiszolgálók levonhatók.
- A függőségek elemzésével biztosítható, hogy semmi ne maradjon hátra, így elkerülhetők az áttelepítés utáni meglepő kimaradások.
- [Tekintse át](common-questions-discovery-assessment.md#what-is-dependency-visualization) a függőségek elemzésével kapcsolatos gyakori kérdéseket.

## <a name="analysis-types"></a>Elemzési típusok

A függőségi elemzések üzembe helyezésére két lehetőség áll rendelkezésre.

**Beállítás** | **Részletek** | **Nyilvános felhő** | **Azure Government**
----  |---- | ----
**Ügynök nélküli** | A VMware-kiszolgálók adatait vSphere API-k használatával kérdezi le.<br/><br/> Nem szükséges ügynököket telepíteni a kiszolgálókra.<br/><br/> Ez a beállítás jelenleg előzetes verzióban érhető el, csak a VMware-kiszolgálókon. | Támogatott. | Támogatott.
**Ügynök-alapú elemzés** | A Azure Monitor [Service Map megoldását](../azure-monitor/vm/service-map.md) használja a függőségi vizualizációk és elemzések engedélyezéséhez.<br/><br/> Az összes elemezni kívánt helyszíni kiszolgálóra telepítenie kell az ügynököket. | Támogatott | Nem támogatott.

## <a name="agentless-analysis"></a>Ügynök nélküli elemzés

Az ügynök nélküli függőség elemzése a TCP-kapcsolatok adatainak a kiszolgálókról történő rögzítésével működik, amelyeken engedélyezve van. Nincsenek ügynökök telepítve a kiszolgálókon. A kiszolgálóval és a folyamattal, valamint a célkiszolgálón, a folyamattal és a porttal létesített kapcsolatok logikailag egy függőségbe vannak csoportosítva. Megjelenítheti a rögzített függőségi adatkészleteket egy Térkép nézetben, vagy exportálhatja CSV-ként. Nincsenek ügynökök telepítve az elemezni kívánt kiszolgálókon.

### <a name="dependency-data"></a>Függőségi adatértékek

A függőségi adatmennyiség megkezdése után a lekérdezés megkezdődik:

- Az Azure Migrate készülék öt percenként lekérdezi a kiszolgálók TCP-kapcsolatainak adatait az adatok összegyűjtéséhez.
- Az adatokat a rendszer a vSphere API-k használatával gyűjti a vendég kiszolgálókról vCenter Serveron keresztül.
- A lekérdezés összegyűjti ezeket az adatokat:

    - Az aktív kapcsolatokkal rendelkező folyamatok neve.
    - Az aktív kapcsolatokkal rendelkező folyamatokat futtató alkalmazás neve.
    - Az aktív kapcsolatok célport.

- Az összegyűjtött adatokat a rendszer a Azure Migrate berendezésen dolgozza fel, az azonosító adatok leforgása érdekében, és hathavonta küldi el Azure Migrate


## <a name="agent-based-analysis"></a>Ügynök-alapú elemzés

Az ügynök-alapú elemzések esetében Azure Migrate: a felderítés és értékelés a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja Azure monitor. Telepítse a [Microsoft monitoring Agent/log Analytics ügynököt](../azure-monitor/agents/agents-overview.md#log-analytics-agent) és a [függőségi ügynököt](../azure-monitor/agents/agents-overview.md#dependency-agent)minden elemezni kívánt kiszolgálón.

### <a name="dependency-data"></a>Függőségi adatértékek

Az ügynök-alapú elemzés a következő adatkészleteket biztosítja:

- Forráskiszolgáló neve, folyamat, alkalmazás neve.
- Célkiszolgáló neve, folyamat, alkalmazásnév és port.
- A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők.

## <a name="compare-agentless-and-agent-based"></a>Ügynök nélküli és ügynök szerinti összehasonlítás

Az ügynök nélküli vizualizáció és az ügynök-alapú vizualizáció közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
**Támogatás** | A csak VMware-kiszolgálók esetében előzetes verzióban érhető el. [Tekintse át](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) a támogatott operációs rendszereket. | Általánosan elérhető (GA).
**Ügynök** | Nincs szükség ügynökökre az elemezni kívánt kiszolgálókon. | Az elemezni kívánt helyszíni kiszolgálókon szükséges ügynökök.
**Naplóelemzés** | Nem szükségesek. | Azure Migrate a függőségi elemzéshez a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) .<br/><br/> Egy Log Analytics munkaterületet egy projekthez társít. A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Folyamat** | A TCP-kapcsolatok adatmennyiségét rögzíti. A felderítést követően öt percen belül gyűjti az adatokat. | A kiszolgálón telepített Service Map ügynökök a TCP-folyamatokkal kapcsolatos adatokat gyűjtenek, valamint az egyes folyamatok bejövő/kimenő kapcsolatait.
**Adatok** | Forráskiszolgáló neve, folyamat, alkalmazás neve.<br/><br/> Célkiszolgáló neve, folyamat, alkalmazásnév és port. | Forráskiszolgáló neve, folyamat, alkalmazás neve.<br/><br/> Célkiszolgáló neve, folyamat, alkalmazásnév és port.<br/><br/> A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők. 
**Vizualizáció** | Az önálló kiszolgálók függőségi térképe egy óra és 30 nap közötti időtartamon belül megtekinthető. | Egyetlen kiszolgáló függőségi térképe.<br/><br/> Kiszolgálók egy csoportjának függőségi térképe.<br/><br/>  A Térkép csak egy órán át jeleníthető meg.<br/><br/> Kiszolgálók hozzáadása és eltávolítása a Térkép nézetből.
Adatexportálás | Az elmúlt 30 nap során az adatletöltés CSV formátumban is letölthető. | Az adatlekérdezés a Log Analytics használatával kérdezhető le.



## <a name="next-steps"></a>Következő lépések

- Ügynök-alapú függőségi vizualizáció [beállítása](how-to-create-group-machine-dependencies.md) .
- [Próbálja ki](how-to-create-group-machine-dependencies-agentless.md) az ügynök nélküli függőségi vizualizációt a VMware-kiszolgálókon.
- Tekintse át a függőségi vizualizációval kapcsolatos [gyakori kérdéseket](common-questions-discovery-assessment.md#what-is-dependency-visualization) .
