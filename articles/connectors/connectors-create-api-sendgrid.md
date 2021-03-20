---
title: Kapcsolódás a SendGrid-hez Azure Logic Apps
description: Automatizálja az e-maileket küldő feladatokat és munkafolyamatokat, és kezelje a SendGrid található levelezési listát Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87290445"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>E-mailek küldése és a SendGrid lévő levelezőlisták kezelése a Azure Logic Apps használatával

A Azure Logic Apps és a SendGrid-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek e-maileket küldenek és kezelhetik a címzettek listáját, például:

* E-mail küldése.
* Címzettek hozzáadása a listához.
* Globális letiltások beolvasása, hozzáadása és kezelése.

A logikai alkalmazásokban a feladatok végrehajtásához SendGrid műveleteket is használhat. Más műveletek is használhatók a SendGrid műveletek kimenetének használatával. 

Ez az összekötő csak műveleteket biztosít, ezért a logikai alkalmazás indításához használjon egy külön eseményindítót, például egy **ismétlődési** eseményindítót. Ha például rendszeresen ad hozzá címzetteket a listához, az Office 365 Outlook Connector vagy a Outlook.com-összekötő használatával küldhet e-mailt a címzettekről és a listáról.
Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Egy [SendGrid-fiók](https://www.sendgrid.com/) és egy [SendGrid API-kulcs](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Az API-kulcs engedélyezi a logikai alkalmazás számára a kapcsolat létrehozását és a SendGrid-fiók elérését.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a SendGrid-fiókját. Ha SendGrid műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-sendgrid"></a>Csatlakozás a SendGridhez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Válasszon egy elérési utat: 

   * Az utolsó lépésben, amelyben hozzá szeretne adni egy műveletet, válassza az **új lépés** lehetőséget. 

     -vagy-

   * A művelethez hozzáadni kívánt lépések között vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "sendgrid" kifejezést. A műveletek listában válassza ki a kívánt műveletet.

1. Adja meg a kapcsolat nevét. 

1. Adja meg a SendGrid API-kulcsát, majd kattintson a **Létrehozás** gombra.

1. Adja meg a kiválasztott művelethez szükséges adatokat, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/sendgrid/).

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
