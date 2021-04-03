---
title: Kapcsolódás a SharePointhoz Azure Logic Apps
description: Automatizálja a SharePoint Online-ban vagy a helyszíni SharePoint-kiszolgálón lévő erőforrásokat figyelő és kezelő feladatokat és munkafolyamatokat Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: c72330792e508361830c1bf391f85eefe78bdd1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283979"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>SharePoint-erőforrások monitorozása és kezelése az Azure Logic Appsszel

A Azure Logic Apps és a SharePoint-összekötővel automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek a SharePoint Online-ban vagy a helyszíni SharePoint-kiszolgálón lévő erőforrásokat (például fájlokat, mappákat, listát, elemeket, személyeket stb.) figyelik és kezelhetik, például:

* A fájlok vagy elemek létrehozásának, módosításának vagy törlésének figyelése.
* Elemek létrehozása, beolvasása, frissítése vagy törlése.
* Mellékletek hozzáadása, beolvasása vagy törlése. A tartalom lekérése a mellékletekből.
* Fájlok létrehozása, másolása, frissítése vagy törlése. 
* Fájl tulajdonságainak frissítése Egy fájl tartalmának, metaadatainak vagy tulajdonságainak beolvasása.
* Mappák listázása vagy kibontása.
* Listák vagy listanézet beolvasása.
* Tartalom-jóváhagyási állapot beállítása
* Személyek feloldása.
* HTTP-kérelmek küldése a SharePointnak.
* Entitás értékének beolvasása.

Használhat olyan eseményindítókat, amelyek válaszokat kapnak a SharePointból, és más műveletek számára elérhetővé teszik a kimenetet. A logikai alkalmazásokban a feladatok a SharePointban való végrehajtásához használhatók. Más műveletek is használhatók a SharePoint-műveletek kimenetének használatával. Ha például rendszeresen letölti a fájlokat a SharePointból, a Slack-összekötő használatával üzeneteket küldhet a csapatának.
Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A SharePoint-webhely címe és a felhasználói hitelesítő adatok

  A hitelesítő adatai engedélyezik a logikai alkalmazásnak a kapcsolat létrehozását és a SharePoint-fiók elérését. 

* Ahhoz, hogy a logikai alkalmazásokat a helyszíni rendszerekhez, például a SharePoint Serverhez lehessen kapcsolni, [telepítenie és be kell állítania egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Ily módon megadhatja, hogy az átjáró telepítését a logikai alkalmazáshoz tartozó SharePoint Server-kapcsolatok létrehozásakor használja.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, amelyhez el szeretné érni a SharePoint-fiókját. Ha SharePoint-triggerrel szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha SharePoint-műveletet szeretne használni, indítsa el a logikai alkalmazást egy triggerrel, például egy Salesforce triggerrel, ha rendelkezik Salesforce-fiókkal.

  Elindíthatja például a logikai alkalmazást a **rekord létrehozásakor** Salesforce trigger használatával. 
  Ez az eseményindító minden alkalommal aktiválódik, amikor egy új rekord, például egy érdeklődő jön létre a Salesforce-ben. 
  Ezt követően ezt az triggert a SharePoint- **fájl létrehozása** művelettel követheti el. Így az új rekord létrehozásakor a logikai alkalmazás létrehoz egy fájlt a SharePointban az új rekorddal kapcsolatos információkkal.

## <a name="connect-to-sharepoint"></a>Kapcsolódás a SharePointhoz

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be szűrőként a "SharePoint" kifejezést. Válassza ki a kívánt eseményindítót az eseményindítók listából. 

   -vagy-

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez SharePoint-műveletet kíván hozzáadni, válassza az **új lépés** lehetőséget. 
   A keresőmezőbe írja be a "SharePoint" kifejezést a szűrőként. 
   A műveletek listában válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. 
   Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. Amikor a rendszer kéri, hogy jelentkezzen be, adja meg a szükséges kapcsolódási adatokat. Ha a SharePoint Servert használja, ügyeljen arra, hogy **a helyszíni adatátjárón keresztül válassza a kapcsolat** lehetőséget. Ha elkészült, kattintson a **Létrehozás** gombra.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](/connectors/sharepoint/).

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

