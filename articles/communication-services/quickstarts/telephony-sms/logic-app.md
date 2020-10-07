---
title: Gyors útmutató – SMS-üzenetek küldése Azure Logic Apps az Azure kommunikációs szolgáltatások használatával
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan küldhet SMS-üzeneteket Azure Logic Apps munkafolyamatokban az Azure Communication Services-összekötő használatával.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/06/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: ed9af178c10d033806b05f73372a0642bb28cab4
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91801651"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Gyors útmutató: SMS-üzenetek küldése Azure Logic Apps az Azure kommunikációs szolgáltatásokkal

Az [Azure kommunikációs szolgáltatások SMS](../../overview.md) -összekötő és a [Azure Logic apps](../../../logic-apps/logic-apps-overview.md)használatával automatizált munkafolyamatokat vagy *logikai alkalmazásokat*hozhat létre, amelyek SMS-üzeneteket küldhetnek. Ebből a rövid útmutatóból megtudhatja, hogyan küldhet automatikusan szöveges üzeneteket egy trigger eseményre adott válaszként, amely a logikai alkalmazás munkafolyamatának első lépése. Az eseményindító esemény lehet egy bejövő e-mail-üzenet, egy ismétlődési ütemterv, egy [Azure Event Grid](../../../event-grid/overview.md) erőforrás-esemény vagy bármely más, [Azure Logic apps által támogatott eseményindító](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="A Logic app Designer számára nyitva álló Azure Portal bemutató képernyőkép, amely egy példaként szolgáló logikai alkalmazást mutat be, amely az SMS küldése műveletet használja az Azure kommunikációs szolgáltatások összekötőhöz.":::

Bár ez a rövid útmutató arra összpontosít, hogy az összekötővel válaszoljon egy triggerre, az összekötővel is reagálhat más műveletekre, amelyek az triggert követő lépések a munkafolyamatban. Ha még nem ismeri a Logic Apps, tekintse át, [Mi az Azure Logic apps az](../../../logic-apps/logic-apps-overview.md) első lépések előtt.

> [!NOTE]
> A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók, vagy [hozzon létre ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Aktív Azure kommunikációs szolgáltatások erőforrás vagy [kommunikációs szolgáltatások erőforrásának létrehozása](../create-communication-resource.md).

- Aktív Logic Apps erőforrás (logikai alkalmazás), vagy [hozzon létre egy üres logikai alkalmazást, de a használni kívánt triggert](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Az Azure kommunikációs szolgáltatások SMS-összekötője jelenleg csak műveleteket biztosít, így a logikai alkalmazásnak legalább egy triggerre van szüksége.

  Ez a rövid útmutató az **új e-mail-üzenet érkezésekor** a triggert használja, amely az [Office 365 Outlook Connector](/connectors/office365/)használatával érhető el.

- SMS-kompatibilis telefonszám vagy [telefonszám beolvasása](./get-phone-number.md).

## <a name="add-an-sms-action"></a>SMS-művelet hozzáadása

Ha az **SMS-küldési** műveletet új lépésként szeretné hozzáadni a munkafolyamatban az Azure kommunikációs szolgáltatások SMS-összekötő használatával, kövesse az alábbi lépéseket [Azure Portal](https://portal.azure.com) a logikai alkalmazás munkafolyamatának megnyitásával a Logic app Designerben:

1. A tervezőben az új művelet hozzáadásának lépése alatt válassza az **új lépés**lehetőséget. Másik lehetőségként a lépések közötti új művelet hozzáadásához vigye a mutatót a fenti lépések között látható nyíl fölé, válassza a pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet** keresőmezőbe írja be a parancsot `Azure Communication Services` . A műveletek listából válassza az **SMS küldése**lehetőséget.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="A Logic app Designer számára nyitva álló Azure Portal bemutató képernyőkép, amely egy példaként szolgáló logikai alkalmazást mutat be, amely az SMS küldése műveletet használja az Azure kommunikációs szolgáltatások összekötőhöz.":::

1. Most hozzon létre egy kapcsolatot a kommunikációs szolgáltatások erőforrásával.

   1. Adja meg a kapcsolatok nevét.

   1. Válassza ki az Azure kommunikációs szolgáltatások erőforrását.

   1. Kattintson a **Létrehozás** gombra.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="A Logic app Designer számára nyitva álló Azure Portal bemutató képernyőkép, amely egy példaként szolgáló logikai alkalmazást mutat be, amely az SMS küldése műveletet használja az Azure kommunikációs szolgáltatások összekötőhöz.":::

1. Az **SMS küldése** műveletben adja meg a következő információkat: 

   * A forrás-és a célhelyek telefonszámai. Tesztelési célokra használhatja a saját telefonszámát célként megadott telefonszámként.

   * Az elküldeni kívánt üzenet tartalma, például: "Hello from Logic Apps!".

   Íme egy **SMS-küldési** művelet, amely például a következő információkat küldi el:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="A Logic app Designer számára nyitva álló Azure Portal bemutató képernyőkép, amely egy példaként szolgáló logikai alkalmazást mutat be, amely az SMS küldése műveletet használja az Azure kommunikációs szolgáltatások összekötőhöz.":::

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés**lehetőséget.

Ezután futtassa a logikai alkalmazást teszteléshez.

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás**lehetőséget. Megvárhatja, amíg a logikai alkalmazás aktiválva van. Mindkét esetben a logikai alkalmazásnak SMS-üzenetet kell küldenie a megadott telefonszámra. A logikai alkalmazás futtatásával kapcsolatos további információkért tekintse át [a logikai alkalmazás futtatását](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app) ismertető témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kommunikációs szolgáltatások előfizetésének eltávolításához törölje a kommunikációs szolgáltatások erőforrását vagy az erőforráscsoportot. Az erőforráscsoport törlése szintén törli az adott csoport többi erőforrását is. További információkért tekintse át a [kommunikációs szolgáltatások erőforrásainak tisztítását](../create-communication-resource.md#clean-up-resources)ismertető témakört.

A logikai alkalmazás munkafolyamatának és a kapcsolódó erőforrásoknak a tisztításához tekintse át [Logic apps erőforrások tisztításának módját](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan küldhet SMS-üzeneteket Azure Logic Apps és az Azure kommunikációs szolgáltatásainak használatával. További információért folytassa az SMS-eseményekre való feliratkozással:

> [!div class="nextstepaction"]
> [Előfizetés SMS-eseményekre](./handle-sms-events.md)

Az SMS-ben az Azure kommunikációs szolgáltatásokban a következő cikkekben talál további információt:

- [SMS-sel kapcsolatos alapfogalmak](../../concepts/telephony-sms/concepts.md)
- [A telefonos és SMS-megoldás megtervezése](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
