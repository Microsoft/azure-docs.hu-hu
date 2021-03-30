---
title: A virtuális gépek adatforgalmi mintáinak figyelése
description: Megtudhatja, hogyan figyelheti az adatváltozási mintákat a védett Virtual Machines Azure Site Recovery használatával
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: aeb89a9d18e4550fa1d6162920d60507fd50c208
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92359866"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>A virtuális gépek adatforgalmi mintáinak figyelése

Ez a cikk áttekintést nyújt a virtuális gépek adatváltozási mintáinak figyelésére szolgáló különböző eszközökről. A megfelelő eszközök használatával könnyen megállapítható, hogy melyik alkalmazás okozza a nagy adatforgalomot, és az adott alkalmazásra vonatkozó további műveleteket is végrehajthat.

## <a name="for-azure-virtual-machines-windows-or-linux"></a>Azure-beli virtuális gépekhez (Windows vagy Linux)

Ha a gép az Azure-ban üzemel, és felügyelt vagy nem felügyelt lemezt használ a tároláshoz, a lemez metrikáinak nyomon követésével könnyedén nyomon követheti a teljesítményt. Ez lehetővé teszi, hogy az alkalmazás használati mintájának megfelelően gondosan figyelje és kiválassza a megfelelő lemezt. Azt is felhasználhatja, hogy riasztásokat, diagnosztikai és automatizálási felépítést hozzon létre. [További információ](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

Ha a gépeket a Azure Site Recovery segítségével védi, a gépeket Azure Monitor naplók és Log Analytics használatával figyelheti. [További információ](./monitor-log-analytics.md).

Vannak olyan operációs rendszerre vonatkozó eszközök is, amelyeket használhat.

## <a name="for-windows-machines"></a>Windowsos gépek esetén

Ha van számítógépe, a helyszínen, vagy nem, amely a Windows operációs rendszert futtatja, akkor van még néhány elérhető eszköz.

A lemez használatának ellenőrzése mellett a Feladatkezelő szolgáltatásban mindig megtekintheti a **erőforrás-figyelő** és a **Teljesítményfigyelőt**. Ezek az eszközök már jelen vannak a Windows rendszerű gépeken.

### <a name="resource-monitor"></a>Erőforrás-figyelő

A **erőforrás-figyelő** valós időben jeleníti meg a hardveres és a szoftveres erőforrások használatáról szóló információkat. A erőforrás-figyelő Windows rendszerű gépen való futtatásához kövesse az alábbi lépéseket –

1. Nyomja meg a Win + R és a _ResMon_ típust.
1. Ha a ResMon, azaz erőforrás-figyelő, az ablak megnyílik a lemez lapra. A következő nézetet nyújtja:

    ![erőforrás-figyelő lemez lap](./media/monitoring-high-churn/resmon-disk-tab.png)

1. Ezt a lapot folyamatosan kell figyelni, hogy egy kis ideig tiszta képet kapjon. A fenti példában azt láthatjuk, hogy a _wmiprv.exe_ magas.

Miután azonosította az alkalmazásokat, amelyek nagy adatváltozást okoznak a gépen, megteheti a szükséges műveleteket az alkalmazásokkal kapcsolatos adatforgalom kezeléséhez.

### <a name="performance-monitor"></a>Teljesítménymonitorozás

A Teljesítményfigyelő különböző tevékenységeket **figyel** a számítógépen, például a CPU-vagy memóriahasználat. A Teljesítményfigyelő Windows rendszerű gépen való futtatásához kövesse az alábbi lépéseket –

1. Nyomja meg a Win + R és a _perfmon_ típust.
1. Ha a perfmon, azaz a Teljesítményfigyelő, ablak nyílik meg, a következő nézetet nyújtja:

    ![Teljesítményfigyelő 1. lépés](./media/monitoring-high-churn/perfmon-step1.png)

1. Bontsa ki a **figyelési eszközök** mappát a jobb oldalon, majd kattintson a Teljesítményfigyelő elemre. Ekkor megnyílik az alábbi nézet, amely valós idejű információkat biztosít az aktuális teljesítményről.

    ![Teljesítményfigyelő 2. lépés](./media/monitoring-high-churn/perfmon-step1.png)

1. Ez a gráf jelenleg csak egy figyelőt figyel, azaz a (z) "% Processor Time"-t, ahogy azt a tábla közvetlenül a gráf alatt mutatja. A figyeléshez további elemeket is hozzáadhat, ha az eszköz tetején a **"+"** elemre kattint.
1. Az alábbiakban látható, hogyan néz ki a Teljesítményfigyelő, ha további számlálókat adunk hozzá –

    ![Teljesítményfigyelő 3. lépés](./media/monitoring-high-churn/perfmon-step3.png)

További tudnivalók a Teljesítményfigyelőről [itt](/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data)talál további információt.

## <a name="for-linux-machines"></a>Linuxos gépek esetén

Ha rendelkezik egy géppel, a helyszínen, vagy nem, amely a Linux operációs rendszert futtatja, akkor további eszközök is elérhetők az adatváltozási minták figyeléséhez.

### <a name="iotop"></a>Iotop

Az egyik leggyakrabban használt eszköz a _iotop_. Ez egy segédprogram a valós idejű lemezes tevékenységek megjelenítéséhez. Listázhatja az I/O-t végző folyamatokat, valamint az általuk használt lemezes sávszélességet is.

Nyissa meg a parancssort, és futtassa a parancsot `iotop` .

### <a name="iostat"></a>IoStat

A IoStat egy egyszerű eszköz, amely összegyűjti és megjeleníti a rendszerszintű és kimeneti tárolóeszköz statisztikáit. Ezt az eszközt gyakran használják a tárolóeszközök teljesítményével kapcsolatos problémák nyomon követésére, beleértve az eszközöket, a helyi lemezeket és a távoli lemezeket.

Nyissa meg a parancssort, és futtassa a parancsot `iostat` .

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan figyelheti [Azure monitor](monitor-log-analytics.md).