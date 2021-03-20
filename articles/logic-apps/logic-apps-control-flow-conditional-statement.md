---
title: Feltételes utasítások hozzáadása munkafolyamatokhoz
description: A Azure Logic Apps munkafolyamataiban műveleteket vezérlő feltételek létrehozása
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 19f42270e37c42cf56bbde04f73c01027a56ff63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89657383"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>A Azure Logic Apps munkafolyamat-műveleteinek vezérlésére szolgáló feltételes utasítások létrehozása

Ha konkrét műveleteket szeretne futtatni a logikai alkalmazásban egy megadott feltétel átadását követően, adjon hozzá egy *feltételes utasítást*. Ez a vezérlőstruktúra összehasonlítja a munkafolyamatban lévő adatokat meghatározott értékekkel vagy mezőkkel. Ezután különböző műveleteket is megadhat, amelyek attól függően futnak, hogy az adott állapot megfelel-e az adott feltételnek. A feltételeket egymásba ágyazhatja.

Tegyük fel például, hogy van egy logikai alkalmazás, amely túl sok e-mailt küld, amikor új elemek jelennek meg a webhely RSS-hírcsatornáján. Hozzáadhat egy feltételes utasítást úgy, hogy csak akkor küldjön e-mailt, ha az új elem egy adott karakterláncot tartalmaz. 

> [!TIP]
> Ha különböző lépéseket szeretne futtatni a különböző konkrét értékek alapján, használjon [*switch utasítást*](../logic-apps/logic-apps-control-flow-switch-statement.md) .

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A jelen cikkben szereplő példának megfelelően [hozza létre ezt a minta logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy Outlook.com-fiókkal vagy egy munkahelyi vagy iskolai fiókkal.

## <a name="add-condition"></a>Feltétel hozzáadása

1. A <a href="https://portal.azure.com" target="_blank">Azure Portalban</a>nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Adjon hozzá egy feltételt a kívánt helyen. 

   Ha fel szeretne venni egy feltételt a lépések között, vigye a mutatót arra a nyílra, ahová a feltételt hozzá szeretné adni. Válassza ki a megjelenő **pluszjelet** ( **+** ), majd válassza a **művelet hozzáadása** lehetőséget. Például:

   ![Művelet hozzáadása a lépések között](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Ha a munkafolyamat végén fel szeretne venni egy feltételt, a logikai alkalmazás alján válassza az  **új lépés** > **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "feltétel" kifejezést. Válassza ki ezt a műveletet: **feltétel – vezérlés**

   ![Feltétel hozzáadása](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. A **feltétel** mezőben hozza létre a feltételt. 

   1. A bal oldali mezőben határozza meg az összehasonlítani kívánt adattípust vagy mezőt.

      Ha a bal oldali mezőbe kattint, megjelenik a dinamikus tartalom lista, így a logikai alkalmazás korábbi lépéseiből származó kimeneteket is kiválaszthat. 
      Ebben a példában válassza az RSS-hírcsatorna összegzése elemet.

      ![A feltétel felépítése](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. A középső mezőben válassza ki a végrehajtani kívánt műveletet. 
   Ebben a példában válassza a "**tartalmazza**" lehetőséget. 

   1. A jobb oldali mezőben adja meg a kívánt értéket vagy mezőt a feltételnek megfelelően. 
   Ebben a példában a következő sztringet kell megadnia: **Microsoft**

   A teljes feltétel:

   ![Teljes feltétel](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Ha további sort szeretne felvenni a feltétellel **, válassza a Hozzáadás**  >  **sor** hozzáadása elemet. 
   Alfeltételekkel rendelkező csoport hozzáadásához **válassza a** Hozzáadás  >  **csoport** hozzáadása lehetőséget. 
   A meglévő sorok csoportosításához jelölje be a sorok jelölőnégyzetét, és válassza a három pontra (...), majd a **csoport készítése** lehetőséget.

1. **Ha az értéke TRUE (igaz** ), és **Ha hamis**, adja hozzá a végrehajtandó lépéseket az alapján, hogy teljesül-e a feltétel. Például:

   ![Feltétel "If true" és "If false" elérési utak esetén](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > A meglévő műveleteket a **IF True** és **hamis** elérési utakon is húzhatja.

1. Mentse a logikai alkalmazást.

Ez a logikai alkalmazás most csak akkor küld leveleket, ha az RSS-hírcsatorna új elemei megfelelnek a feltételnek.

## <a name="json-definition"></a>JSON-definíció

Itt látható a feltételes utasítás mögötti magas szintű kód definíciója:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A szolgáltatásokról és javaslatokról a [Azure Logic apps felhasználói visszajelzéseket ismertető webhelyről](https://aka.ms/logicapps-wish)küldhet vagy szavazhat.

## <a name="next-steps"></a>Következő lépések

* [Lépések futtatása különböző értékek alapján (switch utasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtatási és ismétlési lépések (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveleti állapot alapján (hatókörök)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
