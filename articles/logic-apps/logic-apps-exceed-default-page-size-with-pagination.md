---
title: További elemek vagy rekordok beolvasása tördeléssel
description: A tördelés beállítása az összekötő műveleteinek alapértelmezett méretének túllépése Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87090263"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>További adatok, elemek vagy rekordok beolvasása a Azure Logic Apps tördelésének használatával

Amikor adatokat, elemeket vagy rekordokat olvas be [Azure Logic Appsban](../logic-apps/logic-apps-overview.md)összekötő művelettel, akkor előfordulhat, hogy az eredményhalmaz olyan nagy méretű, hogy a művelet nem adja vissza az összes eredményt egyszerre. Bizonyos műveletek esetében az eredmények száma túllépheti az összekötő alapértelmezett méretét. Ebben az esetben a művelet csak az eredmények első oldalát adja vissza. Például az SQL Server összekötőhöz tartozó **sorok beolvasása** művelet alapértelmezett oldalméret 2048, de más beállításoktól függően változhat.

Egyes műveletek lehetővé teszik egy *tördelési* beállítás bekapcsolását, hogy a logikai alkalmazás több eredményt Kérjen a tördelési korlátig, de az eredményeket egyetlen üzenetként adja vissza, amikor a művelet befejeződik. Ha tördelést használ, meg kell adnia egy *küszöbértéket* , amely a művelet által visszaadni kívánt találatok számát határozza meg. A művelet lekérdezi az eredményeket, amíg eléri a megadott küszöbértéket. Ha az elemek teljes száma kisebb a megadott küszöbértéknél, a művelet lekérdezi az összes eredményt.

A tördelési beállítás bekapcsolásával az eredmények lapjait az összekötő oldalméret alapján kérdezi le. Ez azt jelenti, hogy időnként előfordulhat, hogy a megadott küszöbértéknél több eredményt fog kapni. Például a SQL Server **sorok beolvasása** művelet használatakor, amely támogatja a tördelési beállítást:

* A művelet alapértelmezett oldalának mérete 2048 rekord/oldal.
* Tegyük fel, hogy 10 000-es rekordja van, és legalább a 5000-es rekordot megadja.
* A tördelés megkeresi a rekordok lapjait, így legalább a megadott minimális érték beolvasása után a művelet 6144 rekordokat ad vissza (3 lap x 2048 rekord), nem 5000 rekordokat.

Az alábbi lista néhány olyan összekötőt mutat be, amelyeken túllépheti az adott műveletek alapértelmezett méretét:

* [Azure Blob Storage](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft Teams](/connectors/teams/)
* [Oracle Database](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazás és az a művelet, amelyben be szeretné kapcsolni a tördelést. Ha nem rendelkezik logikai alkalmazással, tekintse [meg az első logikai alkalmazás létrehozását](../logic-apps/quickstart-create-first-logic-app-workflow.md)ismertető útmutatót.

## <a name="turn-on-pagination"></a>Tördelés bekapcsolása

Annak megállapításához, hogy egy művelet támogatja-e a tördelést a Logic app Designerben, ellenőrizze a művelet beállításait a **tördelési** beállításhoz. Ez a példa azt mutatja be, hogyan kapcsolható be a tördelés a SQL Server **sorok beolvasása** művelettel.

1. A művelet jobb felső sarkában válassza a három pontot (**..**.), és válassza a **Beállítások** lehetőséget.

   ![A művelet beállításainak megnyitása](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Ha a művelet támogatja a tördelést, a művelet megjeleníti a **tördelési** beállítást.

1. Módosítsa a **tördelési** beállítást **kikapcsolva** értékről **.** A **küszöbérték** tulajdonságban megadhat egy egész értéket azon eredmények megadásához, amelyekre vissza szeretné állítani a műveletet.

   ![A visszaadni kívánt eredmények minimális számának meghatározása](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Ha elkészült, válassza a **kész** lehetőséget.

## <a name="workflow-definition---pagination"></a>Munkafolyamat-definíció – tördelés

Ha bekapcsolja a funkciót támogató művelet tördelését, a logikai alkalmazás munkafolyamat-definíciója tartalmazza a `"paginationPolicy"` tulajdonságot a `"minimumItemCount"` művelet tulajdonságában szereplő tulajdonsággal együtt `"runtimeConfiguration"` , például:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Támogatás kérése

Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
