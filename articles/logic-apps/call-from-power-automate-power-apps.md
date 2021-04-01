---
title: Logikai alkalmazások meghívása a Power Automate-ből és a PowerAppsből
description: A logikai alkalmazások összekötőként való exportálásával meghívja a Microsoft Power automatizáló folyamatainak logikai alkalmazásait.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91762480"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Logikai alkalmazások meghívása a Power Automate-ből és a PowerAppsből

A Microsoft Power automatizáló és a Microsoft Power apps szolgáltatásból származó logikai alkalmazások meghívásához összekötőként exportálhatja a logikai alkalmazásokat. Ha egy logikai alkalmazást egyéni összekötőként tesz elérhetővé egy automatizálási vagy Power apps-környezetben, akkor a logikai alkalmazás a folyamatokból is meghívható.

Ha át szeretné telepíteni a folyamatot az automatizálásból vagy a Logic Appsra való áttérést, olvassa el a [folyamatok exportálása a Power gyorsbüféból és a Azure Logic apps üzembe helyezése](export-from-microsoft-flow-logic-app-template.md)című témakört.

> [!NOTE]
> Nem minden automatizálási összekötő érhető el Azure Logic Appsban. Csak olyan automatizálási folyamatokat telepíthet át, amelyek Azure Logic Appsban egyenértékű összekötővel rendelkeznek. Például a gomb trigger, a jóváhagyási összekötő és az értesítési összekötő kifejezetten a Power automatizálható. A OpenAPI-alapú folyamatok jelenleg nem támogatottak az exportáláshoz és az üzembe helyezéshez logikai alkalmazás-sablonokként.
>
> * Ha szeretné megkeresni, hogy mely automatizálási összekötők nem rendelkeznek Logic Apps megfelelővel, tekintse meg a [Power automatizáló összekötők](/connectors/connector-reference/connector-reference-powerautomate-connectors)
>
> * Ha szeretné megkeresni, hogy mely Logic Apps összekötők nem rendelkeznek az automatikus automatizálással, tekintse meg az [összekötők Logic apps](/connectors/connector-reference/connector-reference-powerautomate-connectors).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy automatizáló vagy Power apps-licenc.

* Egy logikai alkalmazás, amelybe az exportálási kérelem aktiválva van.

* A Power automatizáló folyamata vagy a Power apps, amelyről a logikai alkalmazást hívni kívánja.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Logikai alkalmazás exportálása egyéni összekötőként

Mielőtt meghívja a logikai alkalmazást a Power automatizáló vagy a Power apps szolgáltatásból, először exportálnia kell a logikai alkalmazást egyéni összekötőként.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal keresőmezőbe írja be a kifejezést `Logic Apps` . Az eredmények alatt, a **szolgáltatások** területen válassza a **Logic apps** lehetőséget.

1. Válassza ki az exportálni kívánt logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza az **Exportálás** lehetőséget.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Képernyőkép a Logic app oldaláról Azure Portalban, a menü &quot;Exportálás&quot; gombjának kiválasztásával.":::

1. Az **Exportálás** ablaktáblán, a **név** mezőben adja meg az egyéni összekötő nevét a logikai alkalmazáshoz. A **környezet** listából válassza ki azt az energiagazdálkodási vagy energiagazdálkodási környezetet, amelyről meg szeretné hívni a logikai alkalmazást. Amikor elkészült, válassza az **OK** lehetőséget.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Képernyőkép a logikai alkalmazás exportálási paneljéről, amely az egyéni összekötő nevének és környezetének kötelező mezőit jeleníti meg.":::

1. Annak ellenőrzéséhez, hogy a logikai alkalmazás exportálása sikeres volt-e, tekintse meg az értesítések ablaktáblát.

### <a name="exporting-errors"></a>Exportálási hibák

Az alábbi hibák akkor fordulnak elő, ha a logikai alkalmazást egyéni összekötőként és a javasolt megoldásként exportálja:

* **Nem sikerült beolvasni a környezeteket. Győződjön meg arról, hogy a fiókja konfigurálva van a Power automatizálás szolgáltatáshoz, majd próbálkozzon újra.**: Ellenőrizze, hogy az Azure-fiókja rendelkezik-e energiagazdálkodási tervvel.

* **Az aktuális logikai alkalmazás nem exportálható. Az exportáláshoz válassza ki azt a logikai alkalmazást, amelyhez kérelem-trigger tartozik.**: Győződjön meg arról, hogy a logikai alkalmazás egy [kérelem-triggerrel](./logic-apps-workflow-actions-triggers.md#request-trigger)kezdődik.

## <a name="connect-to-your-logic-app-from-power-automate"></a>Kapcsolódás a logikai alkalmazáshoz a Power automatizáló használatával

A Power automatizáló folyamattal exportált logikai alkalmazáshoz való kapcsolódáshoz:

1. Jelentkezzen be a [Power Automate](https://flow.microsoft.com) szolgáltatásba.

1. Az **energiagazdálkodás** kezdőlapja menüben válassza a **saját folyamatok** lehetőséget.

1. A **folyamatok** lapon válassza ki azt a folyamatot, amelyhez csatlakozni szeretne a logikai alkalmazáshoz.

1. A folyamat oldala menüjében válassza a **Szerkesztés** lehetőséget.

1. A flow-szerkesztőben válassza **&#43; új lépés** lehetőséget.

1. A **válasszon műveletet** területen a keresőmezőbe írja be a logikai alkalmazás-összekötő nevét. Ha nem szeretné, hogy csak az egyéni összekötők jelenjenek meg a környezetben, akkor az **Egyéni** lapra kattintva szűrheti az eredményeket.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Képernyőkép a Power automatizáló folyamat szerkesztőjéről, amely egy új lépést ad hozzá az egyéni összekötőhöz és az elérhető műveletekhez.":::

1. Válassza ki a logikai alkalmazás-összekötővel végrehajtani kívánt műveletet. 

1. Adja meg a művelet által a logikai alkalmazás-összekötőnek átadott adatokat.

1. A módosítások mentéséhez kattintson a **Save (Mentés**) lehetőségre a Power automatizáló szerkesztőjének menüjében.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Logic Apps szolgáltatásban keresse meg az exportált logikai alkalmazást.

1. Győződjön meg arról, hogy a logikai alkalmazás úgy működik, ahogy a várt a Power automatizáló folyamatában.

## <a name="delete-logic-app-connector-from-power-automate"></a>Logic app-összekötő törlése a Power automatizálva

1. Jelentkezzen be a [Power Automate](https://flow.microsoft.com) szolgáltatásba.

1. Az **energiaellátás-automatizálás** kezdőlapján válassza  &gt; a menü **Egyéni összekötők** elemét.

1. A listában keresse meg az egyéni összekötőt, és válassza az ellipszisek (**...**) gomb &gt; **Törlés** lehetőséget.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Képernyőkép az &quot;egyéni összekötők&quot; oldalról, amely bemutatja a logikai alkalmazás egyéni összekötő-felügyeleti gombját.":::

1. A törlés megerősítéséhez kattintson **az OK gombra**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Kapcsolódás a logikai alkalmazáshoz a Power Appsből

A Power apps-folyamattal exportált logikai alkalmazáshoz való kapcsolódáshoz:

1. Jelentkezzen be a [Power Apps](https://powerapps.microsoft.com/) szolgáltatásba.

1. A **Power apps** kezdőlapján válassza a **folyamatok** lehetőséget a menüben.

1. A **folyamatok** lapon válassza ki azt a folyamatot, amelyhez csatlakozni szeretne a logikai alkalmazáshoz.

1. A folyamat oldalán válassza a **Szerkesztés** lehetőséget a folyamat menüjében.

1. A flow-szerkesztőben kattintson az **új lépés&#43;** gombra.

1. Az új lépésben **válassza a művelet lehetőséget** , majd a keresőmezőbe írja be a logikai alkalmazás-összekötő nevét. Igény szerint szűrheti az eredményeket az **Egyéni** lapon, hogy csak az egyéni összekötők megjelenjenek a környezetben.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Képernyőfelvétel a Power apps flow-Szerkesztőről, amely az egyéni összekötőhöz és az elérhető műveletekhez hozzáadott új lépést mutatja.":::

1. Válassza ki az összekötővel végrehajtani kívánt műveletet. 

1. Adja meg, hogy a művelet milyen adatokat továbbítson a logikai alkalmazás-összekötőnek.

1. A módosítások mentéséhez kattintson a Power apps-szerkesztő menü **Mentés** elemére. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Logic Apps szolgáltatásban keresse meg az exportált logikai alkalmazást.

1. Győződjön meg arról, hogy a logikai alkalmazás a Power apps-folyamatnak megfelelően működik.

## <a name="delete-logic-app-connector-from-power-apps"></a>Logic app-összekötő törlése a Power Appsből

1. Jelentkezzen be a [Power Apps](https://powerapps.microsoft.com) szolgáltatásba.

1. A **Power apps** kezdőlapján **válassza** &gt; a menü **Egyéni összekötők** elemét.

1. A listában keresse meg az egyéni összekötőt, és válassza az ellipszisek (**...**) gomb &gt; **Törlés** lehetőséget.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Képernyőkép a Power apps egyéni összekötői oldaláról, amely a Logic app egyéni összekötő-felügyeleti gombjait mutatja.":::

1. A törlés megerősítéséhez kattintson **az OK gombra**.

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure Logic apps-összekötők számára](../connectors/apis-list.md)
* További információ a [Azure Logic apps](../logic-apps/logic-apps-overview.md)
