---
title: Hibák vizsgálata a ITSMC-irányítópult használatával
description: Ismerje meg, hogyan vizsgálhatja meg a hibákat a IT-szolgáltatásmenedzsmenti csatoló irányítópult használatával.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5e1acf422abf487edda3e871fa99d07212c59b3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039529"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Hibák vizsgálata a ITSMC-irányítópult használatával

Ez a cikk a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) irányítópultról tartalmaz információkat. Az irányítópult segítségével megvizsgálhatja az összekötő állapotát.

## <a name="view-errors"></a>Hibák megtekintése

Az irányítópulton található hibák megtekintése:

1. Válassza a **minden erőforrás** elemet, majd keresse meg **a ügyfélszolgálati (*a munkaterület nevét*)**.

   ![Képernyőkép, amely az Azure-szolgáltatások erőforrásait jeleníti meg.](media/itsmc-definition/create-new-connection-from-resource.png)

2. A bal oldali ablaktábla **munkaterület-adatforrások** területén válassza a **ITSM-kapcsolatok** elemet:

   ![Képernyőfelvétel: a ITSM-kapcsolatok kiválasztása a munkahelyi adatforrások között.](media/itsmc-overview/add-new-itsm-connection.png)

3. Az **Összefoglalás** területen a **it-szolgáltatásmenedzsmenti csatoló** területen válassza az **Összefoglalás megtekintése** lehetőséget:

   ![A megtekintés összegzése gombot megjelenítő képernyőkép.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Ha egy gráf megjelenik a **it-szolgáltatásmenedzsmenti csatoló** területen, válassza ki:

   ![A diagram kijelölését bemutató képernyőkép.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Megjelenik az irányítópult. Ezzel az eszközzel áttekintheti az összekötő állapotát és hibáit.
   
   ![Az irányítópulton látható összekötő állapotát megjelenítő képernyőkép.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Irányítópult-elemek ismertetése

Az irányítópult a ITSM eszköznek ezen az összekötőn keresztül továbbított riasztásokkal kapcsolatos információkat tartalmaz. Az irányítópult négy részre oszlik.

### <a name="created-work-items"></a>Munkaelemek létrehozva 

A **MUNKAelemek létrehozott** területen a gráf és az alábbi táblázat tartalmazza a munkaelemek számát. Ha a gráfot vagy a táblázatot választja, további részleteket láthat a munkaelemekről.

![A létrehozott munkaelemet bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Érintett számítógépek 

A érintett **számítógépek** területen a tábla felsorolja a számítógépeket és a hozzájuk társított munkaelemeket. A táblák sorainak kiválasztásával további részleteket tudhat meg a számítógépekről.

A tábla korlátozott számú sort tartalmaz. Ha az összes sort látni szeretné, válassza az **összes** megjelenítése lehetőséget.

![Az érintett számítógépeket megjelenítő képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Az összekötő állapota 

Az **összekötő állapota** területen a gráf és az alatta lévő tábla az összekötő állapotával kapcsolatos üzeneteket tartalmaz. A táblázatban szereplő gráf vagy sorok kiválasztásával további részleteket tudhat meg az üzenetekről.

A tábla korlátozott számú sort tartalmaz. Ha az összes sort látni szeretné, válassza az **összes** megjelenítése lehetőséget.

Ha többet szeretne megtudni a táblázatban lévő üzenetekről, tekintse meg [ezt a cikket](itsmc-dashboard-errors.md).

![Az összekötő állapotát megjelenítő képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Riasztási szabályok 

A **riasztási szabályok** területen a tábla a észlelt riasztási szabályok számával kapcsolatos információkat tartalmaz. A táblázatban szereplő sorok kiválasztásával további részleteket tudhat meg az észlelt szabályokról.
    
A tábla korlátozott számú sort tartalmaz. Ha az összes sort látni szeretné, válassza az **összes** megjelenítése lehetőséget.

![A riasztási szabályokat bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
