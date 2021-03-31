---
title: Kapcsolódás a Slackhez Azure Logic Apps
description: Automatizálja a fájlokat és a fájlok figyelését, valamint a csatornák, csoportok és üzenetek kezelését a Slack-fiókban Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 75f4cea0e5cd2b0bf42c28cbd81e199115834e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283962"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>A Slack figyelése és kezelése Azure Logic Apps

A Azure Logic Apps és a Slack-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek figyelik a Slack-fájlokat, és kezelhetik a Slack-csatornákat, az üzeneteket, a csoportokat és így tovább, például:

* Figyelés az új fájlok létrehozásakor.
* Csatornák létrehozása, listázása és csatlakoztatása 
* Üzenetek közzététele.
* Hozzon létre csoportokat, és állítsa a ne zavarja meg.

Használhat olyan eseményindítókat, amelyek választ kapnak a Slack-fiókjából, és más műveletek számára elérhetővé teszik a kimenetet. A Slack-fiókkal feladatokat végző műveleteket is használhat. Más műveletek is használhatók a Slack-műveletek kimenetének használatával. Egy új fájl létrehozásakor például az Office 365 Outlook Connector használatával küldhet e-mailt. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A [Slack](https://slack.com/) -fiók és a felhasználói hitelesítő adatok

  A hitelesítő adatai engedélyezik a logikai alkalmazásnak, hogy kapcsolatot hozzon létre, és hozzáférjen a Slack-fiókjához.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Slack-fiókját. A Slack triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A Slack művelet használatához indítsa el a logikai alkalmazást egy eseményindítóval, például egy Slack eseményindítóval vagy egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-slack"></a>Kapcsolódás a Slackhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "Slack" kifejezést. Válassza ki a kívánt eseményindítót az eseményindítók listából. 

   -vagy-

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez műveletet szeretne hozzáadni, válassza az **új lépés** lehetőséget. 
   A keresőmezőbe írja be a "Slack" kifejezést a szűrőként. 
   A műveletek listában válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. 
   Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. Ha a rendszer kéri, hogy jelentkezzen be a Slack-ba, jelentkezzen be a Slack-munkaterületére. 

   ![Bejelentkezés a Slack-munkaterületre](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Engedélyezze a hozzáférést a logikai alkalmazáshoz.

   ![A Slackhez való hozzáférés engedélyezése](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Adja meg a kiválasztott triggerhez vagy művelethez szükséges adatokat. A logikai alkalmazás munkafolyamatának folytatásához adjon hozzá további műveleteket.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/slack/).

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

