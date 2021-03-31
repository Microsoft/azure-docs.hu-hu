---
title: Kapcsolódás a Outlook.com
description: Az e-maileket, naptárakat és névjegyeket kezelő feladatokat és munkafolyamatokat az Azure Logic Apps használatával automatizálhatja a Outlook.com
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75707186"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>E-mailek, naptárak és névjegyek kezelése a Outlook.com-ben Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a [Outlook.com-összekötő](/connectors/outlook/)segítségével logikai alkalmazások létrehozásával automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek a @outlook.com vagy a @hotmail.com fiókját kezelik. Automatizálhatja például ezeket a feladatokat:

* Az e-mailek lekérése, küldése és megválaszolása.
* Értekezletek ütemezhetnek a naptárban.
* Névjegyek hozzáadása és szerkesztése.

Bármely eseményindítóval elindíthatja a munkafolyamatot, például amikor új e-mail érkezik, amikor egy naptári elem frissül, vagy ha egy esemény a különbségi szolgáltatásban történik. Használhatja az eseményindító eseményre válaszoló műveleteket, például e-mailek küldését vagy új naptári esemény létrehozását.

> [!NOTE]
> A Microsoft munkahelyi fiókjával (például) kapcsolatos feladatok automatizálásához @fabrikam.onmicrosoft.com használja az [Office 365 Outlook Connectort](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy [Outlook.com-fiók](https://outlook.live.com/owa/)

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az a logikai alkalmazás, amelyhez el szeretné érni a Outlook.com-fiókját. Ha egy Outlook.com triggerrel szeretné elindítani a munkafolyamatot, [üres logikai alkalmazással](../logic-apps/quickstart-create-first-logic-app-workflow.md)kell rendelkeznie. Ha Outlook.com műveletet szeretne hozzáadni a munkafolyamathoz, a logikai alkalmazásnak már rendelkeznie kell egy triggerrel.

## <a name="add-a-trigger"></a>Eseményindító hozzáadása

Az [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan esemény, amely elindítja a munkafolyamatot a logikai alkalmazásban. Ez a példa a logikai alkalmazás egy "lekérdezés" triggert használ, amely minden új e-mailt keres az e-mail-fiókjában a megadott intervallum és gyakoriság alapján.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A keresőmezőbe írja be szűrőként a "outlook.com" kifejezést. Ebben a példában válassza ki, hogy **mikor érkezik új e-mail**.

1. Ha a rendszer kéri, hogy jelentkezzen be, adja meg a Outlook.com hitelesítő adatait, hogy a logikai alkalmazás csatlakozhasson a fiókjához. Ellenkező esetben, ha a hálózat már létezik, adja meg az trigger tulajdonságainak adatait:

1. A triggerben állítsa be a **gyakoriság** és az **intervallum** értékét.

   Ha például 15 percenként szeretné lekérdezni a triggert, állítsa a **gyakoriságot** **percre**, és állítsa be az **intervallumot** **15**-re.

1. A tervező eszköztárán válassza a **Mentés** lehetőséget, amely menti a logikai alkalmazást.

Az triggerre való reagáláshoz adjon hozzá egy újabb műveletet. Felveheti például a Twilio- **üzenet küldése** műveletet, amely szöveget küld egy e-mail érkezésekor.

## <a name="add-an-action"></a>Művelet hozzáadása

A [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) egy olyan művelet, amelyet a logikai alkalmazás munkafolyamata futtat. Ez a példa logikai alkalmazás e-mailt küld a Outlook.com-fiókjából. A művelet kitöltéséhez egy másik trigger kimenetét használhatja. Tegyük fel például, hogy a logikai alkalmazás a SalesForce használja az **objektum létrehozásakor** trigger. Hozzáadhat **egy e-mail** -Outlook.com, és a Salesforce trigger kimeneteit is használhatja az e-mailben.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Ha egy műveletet a munkafolyamat utolsó lépéseként szeretne hozzáadni, válassza az **új lépés** lehetőséget. 

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "outlook.com" kifejezést. Ebben a példában válassza **az E-mail küldése** lehetőséget. 

1. Ha a rendszer kéri, hogy jelentkezzen be, adja meg a Outlook.com hitelesítő adatait, hogy a logikai alkalmazás csatlakozhasson a fiókjához. Ellenkező esetben, ha a kapcsolat már létezik, adja meg a művelet tulajdonságaira vonatkozó információkat.

1. A tervező eszköztárán válassza a **Mentés** lehetőséget, amely menti a logikai alkalmazást.

## <a name="connector-reference"></a>Összekötő-referencia

A technikai részleteket, például az eseményindítókat, a műveleteket és a korlátozásokat az összekötő hencegő fájlja ismerteti, lásd az [összekötő hivatkozási oldalát](/connectors/outlook/). 

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
