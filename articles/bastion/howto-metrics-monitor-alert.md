---
title: Monitorozás és metrikák konfigurálása Azure Monitor használatával
titleSuffix: Azure Bastion
description: Ismerje meg az Azure Bastion monitorozását, valamint a Azure Monitort használó mérőszámokat, a metrikák, a riasztások és a diagnosztikai naplók használatát az Azure-ban.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/12/2021
ms.author: mialdrid
ms.openlocfilehash: 3a5beba3938b5a845a378ede155f2f64e6baac7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103417943"
---
# <a name="how-to-configure-monitoring-and-metrics-for-azure-bastion-using-azure-monitor"></a>Az Azure Bastion figyelésének és metrikáinak konfigurálása Azure Monitor használatával

Ebből a cikkből megtudhatja, hogyan dolgozhat az Azure Bastion monitorozási és mérőszámai Azure Monitor használatával.

>[!NOTE]
>A **klasszikus metrikák** használata nem ajánlott.
>

## <a name="about-metrics"></a>A metrikák ismertetése

Az Azure Bastion különböző metrikákkal rendelkezik, amelyek elérhetők. A következő táblázat az egyes rendelkezésre álló metrikák kategóriáit és dimenzióit mutatja be.

|**Metrika**|**Kategória**|**Dimenzió (k)**|
| --- | --- | --- |
|Megerősített kommunikációs állapot * *|[Rendelkezésre állás](#availability)|N/A|
|Teljes memória|[Rendelkezésre állás](#availability)|Példány|
|Használt processzor|[Adatforgalom](#traffic)|Példány
|Felhasznált memória|[Adatforgalom](#traffic)|Példány
|Munkamenetek száma|[Teljesítmény](#performance)|Példány|

* * A megerősített kommunikációs állapot csak a 2020 november után üzembe helyezett megerősített gazdagépekre vonatkozik.

### <a name="availability-metrics"></a><a name="availability"></a>Rendelkezésre állási metrikák

#### <a name="bastion-communication-status"></a><a name="communication-status"></a>Megerősített kommunikációs állapot

Megtekintheti az Azure Bastion kommunikációs állapotát, amely az összes olyan példányban összesítve van, amely a megerősített gazdagépet tartalmazza.

* Az **1** érték azt jelzi, hogy a megerősített szolgáltatás elérhető.
* A **0** érték azt jelzi, hogy a megerősített szolgáltatás nem érhető el.

:::image type="content" source="./media/metrics-monitor-alert/communication-status.png" alt-text="A kommunikációs állapotot bemutató képernyőkép.":::

#### <a name="total-memory"></a><a name="total-memory"></a>Teljes memória

Megtekintheti az Azure Bastion teljes memóriáját, amely az egyes megerősített példányok között oszlik el.

:::image type="content" source="./media/metrics-monitor-alert/total-memory.png" alt-text="A teljes memóriát ábrázoló képernyőkép.":::

### <a name="traffic-metrics"></a><a name="traffic"></a>Forgalmi metrikák

#### <a name="used-cpu"></a><a name="used-cpu"></a>Használt processzor

Megtekintheti az Azure Bastion CPU-kihasználtságát, és megoszthatja az egyes megerősített példányok között. A mérőszám figyelése segít az Azure Bastion-t alkotó példányok rendelkezésre állásának és kapacitásának mérésében.

:::image type="content" source="./media/metrics-monitor-alert/used-cpu.png" alt-text="A CPU-t ábrázoló képernyőkép.":::

#### <a name="used-memory"></a><a name="used-memory"></a>Felhasznált memória

Megtekintheti az egyes megerősített példányok memóriájának kihasználtságát, és megoszthatja az egyes megerősített példányok között. A mérőszám figyelése segít az Azure Bastion-t alkotó példányok rendelkezésre állásának és kapacitásának mérésében.

:::image type="content" source="./media/metrics-monitor-alert/used-memory.png" alt-text="A használt memóriát ábrázoló képernyőfelvétel.":::

### <a name="performance-metrics"></a><a name="performance"></a>Teljesítmény-mérőszámok

#### <a name="session-count"></a>Munkamenetek száma

Megtekintheti az aktív munkamenetek számát az egyes munkamenet-típusoknál (RDP és SSH) összesítve. Az egyes Azure-alapú megerősített kapcsolatok számos aktív RDP-és SSH-munkamenetet támogatnak. A mérőszám figyelése segít megérteni, hogy módosítani kell-e a megerősített szolgáltatást futtató példányok számát. Az Azure Bastion-munkamenetek számának támogatásával kapcsolatos további információkért tekintse meg az [Azure Bastion – gyakori kérdések](bastion-faq.md)című témakört.

:::image type="content" source="./media/metrics-monitor-alert/session-count.png" alt-text="A munkamenetek darabszámát ábrázoló képernyőkép.":::

## <a name="how-to-view-metrics"></a><a name="metrics"></a>Mérőszámok megtekintése

1. A metrikák megtekintéséhez navigáljon a megerősített gazdagépre.
1. A **figyelés** listából válassza a **metrikák** lehetőséget.
1. Válassza ki a paramétereket. Ha nincsenek megadva metrikák, kattintson a **metrika hozzáadása** elemre, majd válassza ki a paramétereket.

   * **Hatókör:** Alapértelmezés szerint a hatókör a megerősített gazdagépre van beállítva.
   * **Metrikai névtér:** Standard mérőszámok.
   * **Metrika:** Válassza ki a megtekinteni kívánt metrikát.

1. Ha kijelöl egy mérőszámot, a rendszer az alapértelmezett összesítést alkalmazza. Igény szerint a felosztást is alkalmazhatja, amely a metrikát különböző dimenziókkal jeleníti meg.

## <a name="next-steps"></a>Következő lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.
  
