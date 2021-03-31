---
title: Kapcsolódás a Twilio-hez Azure Logic Apps
description: A globális SMS-, MMS-és IP-üzeneteket kezelő feladatok és munkafolyamatok automatizálása a Twilio-fiókon keresztül a Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d144960972f5a1b45e88cc3a0ea015925cae3b91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288150"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Üzenetek kezelése a Twilio-ben Azure Logic Apps

A Azure Logic Apps és a Twilio-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek a globális SMS-, MMS-és IP-üzeneteket tartalmazó Twilio-üzenetek beolvasására, küldésére és listázására használhatók. Ezekkel a műveletekkel feladatokat végezhet el a Twilio-fiókkal. Más műveletek is használhatók a Twilio műveletek kimenetének használatával. Ha például új üzenet érkezik, az üzenet tartalmát a Slack-összekötővel is elküldheti. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* [Twilio](https://www.twilio.com/): 

  * A Twilio-fiók azonosítója és a [hitelesítési token](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), amelyet a Twilio-irányítópulton talál

    A hitelesítő adatai engedélyezik a logikai alkalmazásnak, hogy kapcsolatot hozzon létre, és hozzáférjen a Twilio-fiókhoz a logikai alkalmazásból. 
    Ha Twilio próbaverziós fiókot használ, csak az *ellenőrzött* telefonszámok számára küldhet SMS-t.

  * Egy ellenőrzött Twilio-telefonszám, amely SMS-t tud küldeni

  * Egy ellenőrzött Twilio-telefonszám, amely SMS-t kaphat

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a Twilio-fiókját. Ha Twilio műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-twilio"></a>Kapcsolódás a Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

     * Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés** lehetőséget. 

       -vagy-

     * A művelethez hozzáadni kívánt lépések között vigye az egérmutatót a lépések közötti nyíl fölé. 
     Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.
     
       A keresőmezőbe írja be szűrőként a "twilio" kifejezést. 
       A műveletek listában válassza ki a kívánt műveletet.

1. Adja meg a kapcsolathoz szükséges adatokat, majd kattintson a **Létrehozás** gombra:

   * A kapcsolódáshoz használandó név
   * Az Ön Twilio-fiókjának azonosítója 
   * Twilio-hozzáférési (hitelesítési) token

1. Adja meg a kiválasztott művelethez szükséges adatokat, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/twilio/).

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
