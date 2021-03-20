---
title: A munkafolyamatok hibáinak elhárítása és diagnosztizálása
description: Megtudhatja, hogyan lehet elhárítani és diagnosztizálni a munkafolyamataiban előforduló problémákat, hibákat és hibákat Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95995046"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>A munkafolyamatok hibáinak elhárítása és diagnosztizálása az Azure Logic Appsben

A logikai alkalmazás olyan információkat hoz létre, amelyek segíthetnek az alkalmazásban felmerülő problémák diagnosztizálásában és hibakeresésében. A logikai alkalmazások diagnosztizálásához tekintse át a munkafolyamat egyes lépéseit a Azure Portalon keresztül. Vagy hozzáadhat néhány lépést egy munkafolyamathoz a futásidejű hibakereséshez.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Triggerek előzményeinek megtekintése

Az egyes logikai alkalmazások futtatása trigger-kísérlettel kezdődik, így ha az trigger nem indul el, kövesse az alábbi lépéseket:

1. Ellenőrizze az trigger állapotát az [trigger előzményeinek ellenőrzésével](../logic-apps/monitor-logic-apps.md#review-trigger-history). Ha további információkat szeretne megtekinteni az eseményindítóval kapcsolatos kísérletekről, válassza ki az eseményindító eseményt, például:

   ![Trigger állapotának és előzményeinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Ellenőrizze az trigger bemeneteit, hogy megbizonyosodjon róla, hogy a várt módon jelenjenek meg. A **bemenetek hivatkozás** alatt válassza ki a hivatkozást, amely a **bemenetek** panelt jeleníti meg.

   Az trigger bemenetei közé tartoznak az indító által várt adatok, és a munkafolyamat indításához szükségesek. Ezeknek az adatoknak a megtekintésével megállapíthatja, hogy helyesek-e az trigger bemenetei, és hogy teljesül-e a feltétel, hogy a munkafolyamat folytatódhat-e.

   Az `feedUrl` itt látható tulajdonsághoz például helytelen az RSS-hírcsatorna értéke:

   ![Hibákra vonatkozó trigger-bemenetek áttekintése](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Ellenőrizze az eseményindítók kimeneteit, ha vannak ilyenek, és ellenőrizze, hogy a várt módon jelenjenek-e meg. A **kimenetek hivatkozás** alatt válassza ki a hivatkozást, amely megjeleníti a **kimenetek** ablaktáblát.

   Az aktiválási kimenetek tartalmazzák azokat az adatokat, amelyeket a trigger a munkafolyamat következő lépéseként továbbít. Ezeknek a kimeneteknek a áttekintése segíthet megállapítani, hogy a munkafolyamat következő lépéseként átadott helyes vagy várt értékek, például:

   ![Az trigger kimenetének áttekintése hibák esetén](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Ha olyan tartalmat talál, amelyet nem ismer fel, ismerkedjen meg a Azure Logic Apps [különböző tartalomtípusokkal](../logic-apps/logic-apps-content-type.md) .

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Futtatási előzmények keresése

Minden alkalommal, amikor az eseményindító egy elemhez vagy eseményhez tüzet hoz létre, a Logic Apps motor létrehoz és futtat egy külön munkafolyamat-példányt minden elemhez vagy eseményhez. Ha a Futtatás sikertelen, az alábbi lépéseket követve áttekintheti, hogy mi történt a Futtatás során, beleértve a munkafolyamat egyes lépéseinek állapotát, valamint az egyes lépések bemeneteit és kimeneteit.

1. Ellenőrizze a munkafolyamat futási állapotát a futtatási [Előzmények ellenőrzésével](../logic-apps/monitor-logic-apps.md#review-runs-history). Ha további információkat szeretne megtekinteni egy sikertelen futtatásról, beleértve az állapotukban futó összes lépést is, válassza a sikertelen futtatást.

   ![Futtatási előzmények megtekintése és a sikertelen Futtatás kiválasztása](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Miután a Futtatás összes lépése megjelenik, bontsa ki az első sikertelen lépést.

   ![Az első sikertelen lépés kibontása](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Ellenőrizze a sikertelen lépés bemeneteit annak megerősítéséhez, hogy a várt módon jelenjenek-e meg.

1. Tekintse át az egyes lépések részleteit egy adott futtatásban. A futtatási **Előzmények** területen válassza ki a vizsgálni kívánt futtatást.

   ![Futtatási előzmények áttekintése](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Logikai alkalmazás futtatási részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Egy adott lépéshez tartozó bemenetek, kimenetek és hibaüzenetek vizsgálatához válassza ki ezt a lépést az alakzat kibontása és a Részletek megjelenítése érdekében. Például:

   ![Lépés részleteinek megtekintése](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Futtatókörnyezet hibakeresésének végrehajtása

Ha segítségre van szüksége a hibakereséshez, diagnosztikai lépéseket adhat hozzá egy logikai alkalmazás munkafolyamatához, valamint az trigger és a futtatási előzmények áttekintéséhez. Hozzáadhat például olyan lépéseket, amelyek a [webhook Tester](https://webhook.site/) szolgáltatást használják, így ELLENŐRIZHETI a http-kérelmeket, és meghatározhatja a pontos méretet, alakot és formátumot.

1. Nyissa meg a [webhook Tester](https://webhook.site/) webhelyét, és másolja a generált egyedi URL-címet.

1. A logikai alkalmazásban vegyen fel egy HTTP POST műveletet, valamint a vizsgálni kívánt szövegtörzset, például egy kifejezést vagy egy másik lépés kimenetét.

1. Illessze be a webhook Tester URL-címét a HTTP POST műveletbe.

1. Ha szeretné áttekinteni, hogyan hozza létre a rendszer a Logic Apps motorból generált kérést, futtassa a logikai alkalmazást, és további részletekért látogasson el a webhook Tester webhelyére.

## <a name="next-steps"></a>Következő lépések

* [A logikai alkalmazás figyelése](../logic-apps/monitor-logic-apps.md)
