---
title: Függőségek elemzése Azure Migrate kiszolgáló értékelése során
description: Ismerteti, hogyan használható a függőségi elemzés az értékeléshez Azure Migrate Server Assessment használatával.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 09/15/2020
ms.openlocfilehash: f5304e7634cfb7b4d5c3c05036c0606ba03295ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589061"
---
# <a name="dependency-analysis"></a>Függőségek elemzése

Ez a cikk a függőségek elemzését ismerteti Azure Migrateban: kiszolgáló értékelése.


A függőségi elemzés azonosítja a felderített helyszíni gépek közötti függőségeket. A következő előnyöket biztosítja: 

- A gépeket csoportokba gyűjtheti értékelés céljából, pontosabban, nagyobb megbízhatósággal.
- Azonosíthatja azokat a gépeket, amelyeknek együtt kell áttelepítenie. Ez különösen akkor hasznos, ha nem biztos benne, hogy mely gépek tartoznak az Azure-ba migrálni kívánt alkalmazás-telepítéshez.
- Meghatározhatja, hogy a gépek használatban vannak-e, és hogy mely gépeket lehet leszerelni az áttelepítés helyett.
- A függőségek elemzésével biztosítható, hogy semmi ne maradjon hátra, így elkerülhetők az áttelepítés utáni meglepő kimaradások.
- [Tekintse át](common-questions-discovery-assessment.md#what-is-dependency-visualization) a függőségek elemzésével kapcsolatos gyakori kérdéseket.


## <a name="analysis-types"></a>Elemzési típusok

A függőségi elemzések üzembe helyezésére két lehetőség áll rendelkezésre.

**Beállítás** | **Részletek** | **Nyilvános felhő** | **Azure Government**
----  |---- | ---- 
**Ügynök nélküli** | A VMware virtuális gépek adatait vSphere API-k használatával kérdezi le.<br/><br/> Nem kell telepítenie az ügynököket a virtuális gépekre.<br/><br/> Ez a beállítás jelenleg előzetes verzióban érhető el, csak VMware virtuális gépek esetén. | Támogatott. | Támogatott.
**Ügynök-alapú elemzés** | A Azure Monitor [Service Map megoldását](../azure-monitor/vm/service-map.md) használja a függőségi vizualizációk és elemzések engedélyezéséhez.<br/><br/> Az összes elemezni kívánt helyszíni gépre telepítenie kell az ügynököket. | Támogatott | Nem támogatott.


## <a name="agentless-analysis"></a>Ügynök nélküli elemzés

Az ügynök nélküli függőség elemzése a TCP-kapcsolatok adatainak rögzítésével működik olyan gépekről, amelyeken engedélyezve van. Nincsenek ügynökök telepítve a virtuális gépeken. A kiszolgálóval és a folyamattal, valamint a célkiszolgálón, a folyamattal és a porttal létesített kapcsolatok logikailag egy függőségbe vannak csoportosítva. Megjelenítheti a rögzített függőségi adatkészleteket egy Térkép nézetben, vagy exportálhatja CSV-ként. Nincsenek ügynökök telepítve az elemezni kívánt gépeken.

### <a name="dependency-data"></a>Függőségi adatértékek

A függőségi adatmennyiség megkezdése után a lekérdezés megkezdődik:

- Az Azure Migrate készülék öt percenként lekérdezi a TCP-kapcsolatok adatait a gépekről az adatok összegyűjtéséhez.
- Az adatok a vendég virtuális gépekről vCenter Serveron keresztül, a vSphere API-k használatával lesznek gyűjtve.
- A lekérdezés összegyűjti ezeket az adatokat:

    - Az aktív kapcsolatokkal rendelkező folyamatok neve.
    - Az aktív kapcsolatokkal rendelkező folyamatokat futtató alkalmazás neve.
    - Az aktív kapcsolatok célport.

- Az összegyűjtött adatokat a rendszer a Azure Migrate berendezésen dolgozza fel, az azonosító adatok leforgása érdekében, és hathavonta küldi el Azure Migrate


## <a name="agent-based-analysis"></a>Ügynök-alapú elemzés

Az ügynök-alapú elemzéshez a Server Assessment a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja Azure monitor. Telepítse a [Microsoft monitoring Agent/log Analytics ügynököt](../azure-monitor/agents/agents-overview.md#log-analytics-agent) és a [függőségi ügynököt](../azure-monitor/agents/agents-overview.md#dependency-agent)minden egyes elemezni kívánt gépen.

### <a name="dependency-data"></a>Függőségi adatértékek

Az ügynök-alapú elemzés a következő adatkészleteket biztosítja:

- Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.
- Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port.
- A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők. 



## <a name="compare-agentless-and-agent-based"></a>Ügynök nélküli és ügynök szerinti összehasonlítás

Az ügynök nélküli vizualizáció és az ügynök-alapú vizualizáció közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
**Támogatás** | Csak a VMware virtuális gépek előzetes verziójában. [Tekintse át](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) a támogatott operációs rendszereket. | Általánosan elérhető (GA).
**Ügynök** | Nincs szükség ügynökökre az elemezni kívánt gépeken. | Az elemezni kívánt helyszíni gépekhez szükséges ügynökök.
**Naplóelemzés** | Nem szükségesek. | Azure Migrate a függőségi elemzéshez a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) .<br/><br/> Log Analytics munkaterületet egy Azure Migrate projekthez társít. A munkaterületnek az USA keleti régiójában, Délkelet-Ázsiában vagy Nyugat-európai régióban kell lennie. A munkaterületnek olyan régióban kell lennie, amelyben a [Service Map támogatott](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Folyamat** | A TCP-kapcsolatok adatmennyiségét rögzíti. A felderítést követően öt percen belül gyűjti az adatokat. | A gépen telepített Service Map ügynökök adatokat gyűjtenek a TCP-folyamatokról, valamint az egyes folyamatok bejövő/kimenő kapcsolatairól.
**Adatok** | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port. | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port.<br/><br/> A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők. 
**Vizualizáció** | Az önálló kiszolgálók függőségi térképe egy óra és 30 nap közötti időtartamon belül megtekinthető. | Egyetlen kiszolgáló függőségi térképe.<br/><br/> Kiszolgálók egy csoportjának függőségi térképe.<br/><br/>  A Térkép csak egy órán át jeleníthető meg.<br/><br/> Kiszolgálók hozzáadása és eltávolítása a Térkép nézetből.
Adatexportálás | Az elmúlt 30 nap során az adatletöltés CSV formátumban is letölthető. | Az adatlekérdezés a Log Analytics használatával kérdezhető le.



## <a name="next-steps"></a>Következő lépések

- Ügynök-alapú függőségi vizualizáció [beállítása](how-to-create-group-machine-dependencies.md) .
- [Próbálja ki](how-to-create-group-machine-dependencies-agentless.md) az ügynök nélküli függőségi vizualizációt a VMWare virtuális gépekhez.
- Tekintse át a függőségi vizualizációval kapcsolatos [gyakori kérdéseket](common-questions-discovery-assessment.md#what-is-dependency-visualization) .
