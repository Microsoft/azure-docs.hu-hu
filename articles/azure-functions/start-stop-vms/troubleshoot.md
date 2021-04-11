---
title: Virtuális gépek indítása/leállítása (előzetes verzió) – problémamegoldás
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani az Azure-beli virtuális gépek indítási/leállítási szolgáltatásának (előzetes verzió) funkciójában észlelt problémákat.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111798"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>A virtuális gépek indításával/leállításával kapcsolatos gyakori problémák elhárítása (előzetes verzió)

Ez a cikk a virtuális gépek indítása és leállítása (előzetes verzió) telepítésekor és konfigurálásakor esetlegesen előforduló problémák elhárításával és megoldásával kapcsolatos információkat tartalmaz. Általános információk: [virtuális gépek indítása/leállítása – áttekintés](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Általános ellenőrzés és hibaelhárítás

Ez a szakasz ismerteti, hogyan lehet elhárítani az ütemezett forgatókönyvekkel kapcsolatos általános problémákat, és segít azonosítani a kiváltó okot.

### <a name="azure-dashboard"></a>Azure-irányítópult

Először tekintse át az Azure megosztott irányítópultot. A Start/Stop VM v2 (előzetes verzió) részeként üzembe helyezett Azure megosztott irányítópult gyorsan és egyszerűen ellenőrizheti a virtuális gépeken végrehajtott műveletek állapotát. Tekintse át a virtuális gépek csempén a **közelmúltban megkísérelt műveleteket** a virtuális gépeken végrehajtott legutóbbi műveletek megtekintéséhez. Az adatok a jelentésben való lekérése során némi késéssel, körülbelül öt percen belül megjelennek a Application Insights-erőforrásból.

### <a name="logic-apps"></a>Logic Apps

Attól függően, hogy Logic Apps engedélyezte a kezdő/leállítási forgatókönyv támogatását, tekintse át a futtatási előzményeit, hogy megtudja, miért nem fejeződött be sikeresen az ütemezett indítási/leállítási forgatókönyv egy vagy több cél virtuális gép esetében. Ha szeretné megtudni, hogyan tekintheti át ezt részletesen, tekintse meg [Logic apps futtatási előzményeket](../../logic-apps/monitor-logic-apps.md#review-runs-history).

### <a name="azure-storage"></a>Azure Storage

A virtuális gépeken végrehajtott műveletek részleteit áttekintheti az Azure Storage-fiók Start/Stop VM v2 (előzetes verzió) szolgáltatásban használt **requestsstoretable** . A rekordok megtekintéséhez hajtsa végre a következő lépéseket.

1. Navigáljon a Storage-fiókhoz a Azure Portal és a fiókban válassza a * * Storage Explorer (előzetes verzió) lehetőséget a bal oldali ablaktáblán.
1. Válassza a **táblák** lehetőséget, majd válassza a **requeststoretable** lehetőséget.
1. A tábla minden rekordja az Azure-beli virtuális gépen végrehajtott indítási/leállítási műveletet jelenti a logikai alkalmazás forgatókönyvében meghatározott cél hatókör alapján. Az eredményeket a rekordok egyik tulajdonsága (például IDŐBÉLYEG, művelet vagy TARGETTOPLEVELRESOURCENAME) alapján szűrheti.

### <a name="azure-functions"></a>Azure Functions

Áttekintheti a virtuális gép elindításához és leállításához szükséges összes Azure Functions legfrissebb Meghívási részleteit. Először tekintsük át a végrehajtás folyamatát.

Az **ütemezett** **és az** előkészített forgatókönyv végrehajtási folyamatát ugyanaz a függvény vezérli. A hasznos adatok sémája határozza meg, hogy milyen forgatókönyvet hajt végre a rendszer. Az **ütemezett** forgatókönyv esetén a végrehajtási folyamat a következő: **ütemezett** HTTP > **VirtualMachineRequestOrchestrator** üzenetsor > **VirtualMachineRequestExecutor** -várólista.

A logikai alkalmazásban az **ütemezett** http-függvényt a hasznos adatok sémája hívja meg. Miután az **ütemezett** http-függvény megkapja a kérést, elküldi az adatokat a **Orchestrator** Queue függvénynek, amely az egyes virtuális gépekhez több várólistát hoz létre a művelet végrehajtásához.

A Meghívási részletek megtekintéséhez hajtsa végre a következő lépéseket.

1. A Azure Portal navigáljon a **Azure functions**.
1. Válassza ki a listából a virtuális gépek v2 (előzetes verzió) indítási/leállítási funkcióját.
1. Válassza a **függvények** lehetőséget a bal oldali ablaktáblán.
1. A listában számos, az egyes forgatókönyvekhez társított függvény jelenik meg. Válassza ki az **ütemezett** http-függvényt.
1. Válassza a **figyelő** lehetőséget a bal oldali ablaktáblán.
1. Válassza ki a legutóbbi végrehajtás nyomkövetését, és tekintse meg a Meghívási részleteket és az üzenet szakaszt a részletes naplózáshoz.
1. Ismételje meg ugyanezeket a lépéseket a végrehajtási folyamat korábbi áttekintésének részeként leírt függvényeknél.

További információ a Azure Functions figyeléséről: [Azure functions telemetria elemzése Application Insights](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions és a Logic apps figyeléséről:

* [Azure functions figyelése](../../azure-functions/functions-monitoring.md).

* A [Azure functions figyelésének konfigurálása](../../azure-functions/configure-monitoring.md).

* [Logikai alkalmazások figyelése](../../logic-apps/monitor-logic-apps.md).