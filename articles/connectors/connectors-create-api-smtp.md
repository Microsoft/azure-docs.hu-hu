---
title: Kapcsolódás az SMTP-hez Azure Logic Apps
description: Automatizálja a feladatokat és munkafolyamatokat, amelyek e-mailt küldenek az SMTP (Simple Mail Transfer Protocol) fiók használatával Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87283945"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>E-mail küldése az SMTP-fiókból Azure Logic Apps

A Azure Logic Apps és a Simple Mail Transfer Protocol (SMTP) összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek e-mailt küldenek az SMTP-fiókjából. Más műveletek is használhatók az SMTP-műveletek kimenetének használatával. Ha például az SMTP e-mailt küld, akkor a Slack-összekötővel értesítheti a csapatot a Slackben. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az SMTP-fiók és a felhasználói hitelesítő adatok

  A hitelesítő adatai engedélyezik a logikai alkalmazásnak a kapcsolat létrehozását és az SMTP-fiók elérését.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni az SMTP-fiókját. Ha SMTP-műveletet szeretne használni, indítsa el a logikai alkalmazást egy triggerrel, például egy Salesforce triggerrel, ha rendelkezik Salesforce-fiókkal.

  Elindíthatja például a logikai alkalmazást a **rekord létrehozásakor** Salesforce trigger használatával. 
  Ez az eseményindító minden alkalommal aktiválódik, amikor egy új rekord, például egy érdeklődő jön létre a Salesforce-ben. 
  Ezt követően ezt az triggert az SMTP **E-mail küldése** művelettel követheti el. Így az új rekord létrehozásakor a logikai alkalmazás e-mailt küld az SMTP-fiókjából az új rekordról.

## <a name="connect-to-smtp"></a>Kapcsolódás az SMTP-hez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Az utolsó lépésben, amelyben hozzá szeretne adni egy SMTP-műveletet, válassza az **új lépés** lehetőséget. 

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. 
   Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A keresőmezőbe írja be a "SMTP" kifejezést a szűrőként. A műveletek listában válassza ki a kívánt műveletet.

1. Ha a rendszer kéri, adja meg a következő kapcsolódási adatokat:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Kapcsolat neve** | Yes | Az SMTP-kiszolgálóhoz való kapcsolódás neve | 
   | **SMTP-kiszolgáló címe** | Yes | Az SMTP-kiszolgáló címe | 
   | **Felhasználónév** | Yes | Az SMTP-fiókhoz tartozó Felhasználónév | 
   | **Jelszó** | Yes | Az SMTP-fiók jelszava | 
   | **SMTP-kiszolgáló portja** | No | Egy megadott port a használni kívánt SMTP-kiszolgálón | 
   | **Engedélyezi az SSL használatát?** | No | A TLS/SSL titkosítás bekapcsolása vagy kikapcsolása. | 
   |||| 

1. Adja meg a kiválasztott művelethez szükséges adatokat. 

1. Mentse a logikai alkalmazást, vagy folytassa a logikai alkalmazás munkafolyamatának összeállításával.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/smtpconnector/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
