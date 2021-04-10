---
title: Kapcsolódás az Azure Event Hubshoz
description: Az Azure Event Hubs és Azure Logic Apps használatával olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik és kezelik az eseményeket.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 65da8e07c01561577fe7eff449bfc10348c7f277
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101716601"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Események monitorozása, fogadása és küldése az Azure Event Hubs és az Azure Logic Apps használatával

Ez a cikk bemutatja, hogyan figyelheti és kezelheti az [azure Event Hubsnak](../event-hubs/event-hubs-about.md) eljuttatott eseményeket egy logikai alkalmazásból az Azure Event Hubs Connector használatával. Így olyan logikai alkalmazásokat hozhat létre, amelyek automatizálják az események ellenőrzési, küldési és fogadási feladatait és munkafolyamatait az Event Hubon. Az összekötő-specifikus technikai információk az [Azure Event Hubs-összekötő dokumentációjában](/connectors/eventhubs/)olvashatók </a> .

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* [Azure Event Hubs névtér és Event hub](../event-hubs/event-hubs-create.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni az Event hub-t. A logikai alkalmazás Azure Event Hubs triggerrel való indításához [üres logikai alkalmazásra](../logic-apps/quickstart-create-first-logic-app-workflow.md)van szükség.
Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a [Mi az Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)lehetőséget.

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Engedélyek keresése és a kapcsolatok karakterláncának beolvasása

Győződjön meg arról, hogy a logikai alkalmazás hozzáfér az Event hub-hoz, ellenőrizze az engedélyeket, és szerezze be a Event Hubs névtérhez tartozó kapcsolati karakterláncot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg a Event Hubs *névteret*, nem egy adott Event hub-t. 

1. A névtér menü **Beállítások** területén válassza a **megosztott elérési házirendek** elemet. A **jogcímek** területen győződjön meg arról, hogy rendelkezik az adott névtérhez **tartozó jogosultságokkal** .

   ![Az Event hub-névtér engedélyeinek kezelése](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Ha később manuálisan szeretné megadni a kapcsolódási adatokat, szerezze be a Event Hubs névtérhez tartozó kapcsolódási karakterláncot.

   1. A **házirend** területen válassza a **RootManageSharedAccessKey** lehetőséget.

   1. Keresse meg az elsődleges kulcshoz tartozó kapcsolatok sztringjét. Válassza a másolás gombot, és mentse a kapcsolatok karakterláncát későbbi használatra.

      ![Event Hubs névtérbeli kapcsolatok karakterláncának másolása](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Annak ellenőrzéséhez, hogy a kapcsolati karakterlánc társítva van-e a Event Hubs névteréhez vagy egy adott Event hub-hoz, győződjön meg arról, hogy a kapcsolati karakterlánc nem rendelkezik a `EntityPath` paraméterrel. Ha ezt a paramétert találja, a kapcsolati karakterlánc egy adott Event hub "Entity" értékre vonatkozik, és nem a logikai alkalmazáshoz használandó helyes sztring.

1. Most folytassa a [Event Hubs-trigger hozzáadásával](#add-trigger) vagy [egy Event Hubs művelet hozzáadásával](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Event Hubs-trigger hozzáadása

Azure Logic Apps minden logikai alkalmazásnak egy [eseményindítóval](../logic-apps/logic-apps-overview.md#logic-app-concepts)kell kezdődnie, amely akkor következik be, amikor egy adott esemény történik, vagy ha egy adott feltétel teljesül. A Logic Apps motor létrehoz egy Logic app-példányt, és elindítja az alkalmazás munkafolyamatát.

Ebből a példából megtudhatja, hogyan indíthat el egy logikai alkalmazás-munkafolyamatot, amikor új eseményeket továbbítanak az Event hub-nak. 

> [!NOTE]
> Az Event hub összes eseményindítója *hosszú lekérdezési* eseményindítókat használ, ami azt jelenti, hogy az eseményindító feldolgozza az összes eseményt, majd egy partíción 30 másodpercet vár, hogy további események jelenjenek meg az Event hub-ban. Tehát ha az trigger négy partícióval van beállítva, ez a késleltetés akár két percet is igénybe vehet, mielőtt az aktiválás befejezi az összes partíció lekérdezését. Ha nem érkezik esemény ezen a késésen belül, a rendszer kihagyja a trigger futtatását. Ellenkező esetben az eseményindító addig folytatja az eseményeket, amíg az Event hub üres nem lesz. A következő eseményindító-lekérdezés az eseményindító tulajdonságaiban megadott ismétlődési időköz alapján történik.

1. A Azure Portal vagy a Visual Studióban hozzon létre egy üres logikai alkalmazást, amely megnyitja Logic Apps designert. Ez a példa a Azure Portal használja.

1. A keresőmezőbe írja be szűrőként az "Event hubok" kifejezést. Az eseményindítók listából válassza ki ezt az eseményindítót: **Ha az Event hub eseményei elérhetők a Event Hubs**

   ![Trigger kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a Event Hubs-kapcsolatát](#create-connection). 

1. Adja meg a figyelni kívánt Event hub adatait az Eseményindítóban. A további tulajdonságok megjelenítéséhez nyissa meg az **új paraméterek hozzáadása** listát. Egy paraméter kiválasztásával hozzáadja ezt a tulajdonságot az trigger kártyához.

   ![Eseményindító tulajdonságai](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Eseményközpont neve** | Yes | A figyelni kívánt Event hub neve |
   | **Tartalom típusa** | No | Az esemény tartalomtípusa. A mező alapértelmezett értéke: `application/octet-stream`. |
   | **Fogyasztói csoport neve** | No | Az események olvasásához használandó [Event hub-beli fogyasztói csoport neve](../event-hubs/event-hubs-features.md#consumer-groups) . Ha nincs megadva, a rendszer az alapértelmezett fogyasztói csoportot használja. |
   | **Események maximális száma** | No | Az események maximális száma. Az trigger az egyik és a tulajdonság által megadott események száma közötti értéket adja vissza. |
   | **Intervallum** | Yes | Pozitív egész szám, amely leírja, hogy a munkafolyamat milyen gyakran fut a gyakoriság alapján |
   | **Gyakoriság** | Yes | Az ismétlődés időegysége |
   ||||

   **További tulajdonságok**

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Tartalmi séma** | No | Az Event hub-ból beolvasni kívánt események JSON-tartalmának sémája. Ha például megadja a tartalmi sémát, a logikai alkalmazást csak azokhoz az eseményekhez aktiválhatja, amelyek megfelelnek a sémának. |
   | **Minimális partíciós kulcs** | No | Adja meg az olvasni kívánt [partíció](../event-hubs/event-hubs-features.md#partitions) -azonosító minimális értéket. Alapértelmezés szerint a rendszer az összes partíciót beolvassa. |
   | **Partíció maximális kulcsa** | No | Adja meg az olvasni kívánt [partíció](../event-hubs/event-hubs-features.md#partitions) maximális azonosítóját. Alapértelmezés szerint a rendszer az összes partíciót beolvassa. |
   | **Időzóna** | No | Csak akkor érvényes, ha megad egy kezdési időpontot, mert ez az trigger nem fogad el UTC-eltolást. Válassza ki az alkalmazni kívánt időzónát. <p>További információ: [ismétlődő feladatok és munkafolyamatok létrehozása és futtatása Azure Logic Appssal](../connectors/connectors-native-recurrence.md). |
   | **Kezdési idő** | No | Adja meg a kezdő időpontot a következő formátumban: <p>ÉÉÉÉ-hh-NNTóó: PP: mm, ha időzónát választ<p>-vagy-<p>ÉÉÉÉ-hh-NNTóó: PP: ssZ, ha nem jelöl ki időzónát<p>További információ: [ismétlődő feladatok és munkafolyamatok létrehozása és futtatása Azure Logic Appssal](../connectors/connectors-native-recurrence.md). |
   ||||

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

1. Most folytassa a logikai alkalmazáshoz egy vagy több művelet hozzáadását azokkal a feladatokkal, amelyeket el szeretne végezni az trigger eredményeivel. 

   Ha például egy adott érték (például egy kategória) alapján szeretne eseményeket szűrni, hozzáadhat egy feltételt, hogy az **esemény küldése** művelet csak azokat az eseményeket küldje el, amelyek megfelelnek a feltételnek. 

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Event Hubs-művelet hozzáadása

Azure Logic Apps a [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan lépés a munkafolyamatban, amely egy triggert vagy egy másik műveletet követ. Ebben a példában a logikai alkalmazás egy Event Hubs eseményindítóval kezdődik, amely az Event hub új eseményeit ellenőrzi.

1. A Azure Portal vagy a Visual Studióban nyissa meg a logikai alkalmazást Logic Apps Designerben. Ez a példa a Azure Portal használja.

1. Az trigger vagy a művelet alatt válassza az **új lépés** lehetőséget.

   A meglévő lépések közötti művelet hozzáadásához vigye az egeret a csatlakozás nyíl fölé. 
   Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként az "Event hubok" kifejezést.
A műveletek listából válassza a következő műveletet: **esemény küldése – Event Hubs**

   ![Az "esemény küldése" művelet kiválasztása](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Ha a rendszer megkérdezi a kapcsolat részleteit, [hozza létre most a Event Hubs-kapcsolatát](#create-connection). 

1. A műveletben adja meg az elküldeni kívánt eseményekkel kapcsolatos információkat. A további tulajdonságok megjelenítéséhez nyissa meg az **új paraméterek hozzáadása** listát. Egy paraméter kiválasztásával hozzáadja ezt a tulajdonságot a műveleti kártyához.

   ![Válassza ki az Event hub nevét, és adja meg az esemény tartalmát](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Eseményközpont neve** | Yes | Az Event hub, ahová el szeretné küldeni az eseményt |
   | **Tartalom** | No | Az elküldeni kívánt esemény tartalma |
   | **Tulajdonságok** | No | A küldendő alkalmazás tulajdonságai és értékei |
   | **Partíciókulcs** | No | Az esemény küldési helyének [partíció](../event-hubs/event-hubs-features.md#partitions) -azonosítója |
   ||||

   Elküldheti például a kimenetet a Event Hubs eseményindítóból egy másik Event hubhoz:

   ![Példa küldése eseményre](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés** lehetőséget.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Kapcsolódás az Event hub-hoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Ha a rendszer a kapcsolódási adatok megadását kéri, adja meg a következő adatokat:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Yes | <*kapcsolattípus*> | A kapcsolódáshoz létrehozandó név |
   | **Event Hubs névtér** | Yes | <*Event-hubok – névtér*> | Válassza ki a használni kívánt Event Hubs névteret. |
   |||||  

   Például:

   ![Event hub-kapcsolat létrehozása](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   A kapcsolódási karakterlánc manuális megadásához válassza a **kapcsolódási adatok manuális megadása** lehetőséget. 
   Megtudhatja [, hogyan keresheti meg a kapcsolódási karakterláncot](#permissions-connection-string).

2. Válassza ki a használni kívánt Event Hubs szabályzatot, ha még nincs kiválasztva. Válassza a **Létrehozás** lehetőséget.

   ![Event hub-kapcsolat létrehozása, 2. rész](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. A kapcsolat létrehozása után folytassa a [Event Hubs trigger hozzáadása](#add-trigger) vagy [Event Hubs művelet hozzáadása](#add-action)lehetőséggel.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő hencegő fájlja ismerteti, lásd az [összekötő hivatkozási oldalát](/connectors/eventhubs/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
