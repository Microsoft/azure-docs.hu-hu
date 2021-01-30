---
title: Hibák vizsgálata az irányítópult használatával
description: Ez a dokumentum a ITSMC-irányítópulton előforduló hibákról tartalmaz információkat.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089275"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Hibák vizsgálata a ITSMC-irányítópult használatával

Ez a cikk a IT-szolgáltatásmenedzsmenti csatoló (ITSMC) irányítópultról tartalmaz információkat. Az irányítópult segítségével megvizsgálhatja a ITSMC állapotát.

## <a name="view-the-dashboard"></a>Az irányítópult megtekintése

Az irányítópult megnyitásához kövesse az alábbi lépéseket.

1. Válassza a **minden erőforrás** elemet, majd keresse meg **a ügyfélszolgálati (*a munkaterület nevét*)**.

   ![Képernyőkép, amely az Azure-szolgáltatások erőforrásait jeleníti meg.](media/itsmc-definition/create-new-connection-from-resource.png)

1. A bal oldali ablaktáblán válassza a **munkaterület-adatforrások** lehetőséget, majd válassza az **ITSM kapcsolatok** elemet.

   ![Képernyőfelvétel: a ITSM-kapcsolatok kiválasztása a munkahelyi adatforrások között.](media/itsmc-overview/add-new-itsm-connection.png)

1. Az összefoglalás **szakaszban az** összefoglalás **megtekintése** elemre kattintva megtekintheti az összegző diagramot.

    ![Az összefoglalás szakaszban az összefoglalás megtekintése lehetőséget megjelenítő képernyőkép.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Válassza ki a diagramot az **Összefoglalás** szakaszban az irányítópult megnyitásához.

    ![Az összegző gráf kijelölését bemutató képernyőkép.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Tekintse át az irányítópult állapotát, valamint az összekötővel kapcsolatos esetleges hibákat.
    ![Az irányítópultot megjelenítő képernyőkép.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Irányítópult-elemek ismertetése

Az irányítópult a ITSM eszközbe a jelen összekötő használatával továbbított riasztásokkal kapcsolatos információkat tartalmaz.

Az irányítópult négy szakaszra oszlik:

- **Létrehozott MUNKAelemek**: a gráf és a tábla a munkaelemek típus szerinti számát jeleníti meg. Válassza ki a gráfot vagy a táblázatot, ahol többet tudhat meg a munkaelemekről.
      ![A munkaelemek létrehozásáról szóló szakaszt bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- Érintett **számítógépek**: a tábla a munkaelemeket létrehozó konfigurációs elemek részleteit tartalmazza.
      A konfigurációs elemekkel kapcsolatos további részletekért válassza a táblák sorait.
      A tábla korlátozott számú sort tartalmaz. A teljes lista megtekintéséhez válassza az **összes** megjelenítése lehetőséget.
      ![A befolyásolt számítógépek szakaszt bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **Összekötő állapota**: a gráf és a tábla az összekötő állapotával kapcsolatos információkat jeleníti meg. További részletekért válassza ki a diagramot vagy az üzeneteket a táblázatban. A tábla korlátozott számú sort mutat be. A teljes lista megtekintéséhez válassza az **összes** megjelenítése lehetőséget.
      ![Képernyőkép, amely az összekötő állapota szakaszt jeleníti meg.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **Riasztási szabályok**: Ez a szakasz a észlelt riasztási szabályok számával kapcsolatos információkat tartalmazza. Az észlelt szabályokkal kapcsolatos további részletekért válassza a táblák sorait. A tábla korlátozott számú sorral rendelkezik. A teljes lista megtekintéséhez válassza az **összes** megjelenítése lehetőséget.
      ![A riasztási szabályok szakaszt bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Következő lépések

Tekintse meg az [Összekötők állapotával kapcsolatos gyakori hibákat](itsmc-dashboard-errors.md).
