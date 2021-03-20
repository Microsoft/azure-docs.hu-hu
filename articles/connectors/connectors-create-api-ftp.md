---
title: Kapcsolódás FTP-kiszolgálóhoz
description: Az FTP-kiszolgálón található fájlok létrehozásával, figyelésével és kezelésével kapcsolatos feladatok és munkafolyamatok automatizálása Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999577"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>FTP-fájlok létrehozása, monitorozása és kezelése az Azure Logic Appsszel

A Azure Logic Apps és az FTP-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek fájlok létrehozására, figyelésére, küldésére és fogadására használhatók a fiókjában egy FTP-kiszolgálón, más műveletekkel együtt, például:

* A fájlok hozzáadásának vagy módosításának figyelése.
* Fájlok lekérése, létrehozása, másolása, frissítése, listázása és törlése.
* Fájl tartalmának és metaadatainak beolvasása.
* Archívumok kinyerése mappákba.

Használhat olyan eseményindítókat, amelyek válaszokat kapnak az FTP-kiszolgálóról, és más műveletek számára elérhetővé teszik a kimenetet. Az FTP-kiszolgálón található fájlok kezelésére a Logic apps futtatási műveletei használhatók. Más műveletek is használhatók az FTP-műveletek kimenetének használatával. Ha például rendszeresen letölti a fájlokat az FTP-kiszolgálóról, e-mailt küldhet a fájlokról és azok tartalmáról az Office 365 Outlook Connector vagy a Outlook.com Connector használatával. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Korlátozások

* Az FTP-összekötő csak a TLS/SSL (FTPS) protokollon keresztüli explicit FTP-t támogatja, és nem kompatibilis az implicit FTPS.

* Alapértelmezés szerint az FTP-műveletek a *50 MB vagy annál kisebb* fájlokat képesek olvasni vagy írni. Az 50 MB-nál nagyobb fájlok kezeléséhez az FTP-műveletek támogatják az [üzenetek darabolását](../logic-apps/logic-apps-handle-large-messages.md). A **fájl tartalmának beolvasása** művelet implicit módon adatdarabolást használ.

* Az FTP-eseményindítók nem támogatják a darabolást. Fájl tartalmának kérésekor a triggerek csak 50 MB vagy annál kisebb fájlokat választanak ki. A 50 MB-nál nagyobb fájlok lekéréséhez kövesse az alábbi mintát:

  * Használjon olyan FTP-triggert, amely a fájl tulajdonságait adja vissza, például **egy fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

  * Hajtsa végre az aktiválást az FTP- **Fájl letöltése** művelettel, amely beolvassa a teljes fájlt, és implicit módon használja a darabolást.

* Ha helyszíni FTP-kiszolgálóval rendelkezik, vegye fontolóra egy [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozását vagy [Azure app Service hibrid kapcsolat](../app-service/app-service-hybrid-connections.md)használatát, amely lehetővé teszi a helyszíni adatforrások helyszíni adatátjáró használata nélküli elérését.

## <a name="how-ftp-triggers-work"></a>Az FTP-eseményindítók működése

Az FTP-eseményindítók az FTP fájlrendszer lekérdezésével és a legutóbbi lekérdezés óta módosult fájlok keresésével működnek. Egyes eszközök lehetővé teszik, hogy a fájlok változásakor őrizze meg az időbélyeget. Ezekben az esetekben le kell tiltania ezt a funkciót, így az trigger működhet. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Művelet |
|-------------|--------|
| WinSCP | Ugrás a **Beállítások**  >  **Beállítások**  >  **átvitel**  >  **szerkesztési**  >  **megőrzési időbélyegének**  >  **letiltása** |
| Filezillát | Ugrás az   >  **átvitt fájlok adatmegőrzési időbélyegére –**  >  **Letiltás** |
|||

Ha egy trigger új fájlt talál, az trigger ellenőrzi, hogy az új fájl elkészült-e, és nem részlegesen van-e írva. Előfordulhat például, hogy egy fájl változása folyamatban van, amikor az trigger ellenőrzi a fájlkiszolgálón. Egy részlegesen megírt fájl visszaadásának elkerüléséhez az trigger megállapítja a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az trigger csak akkor adja vissza a fájlt, ha újra kérdezi le a kiszolgálót. Előfordulhat, hogy ez a viselkedés egy késleltetést okoz, amely akár kétszer is meghaladhatja az aktiválás lekérdezési időközét.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az FTP-gazda kiszolgálójának címe és a fiók hitelesítő adatai

  Az FTP-összekötő megköveteli, hogy az FTP-kiszolgáló elérhető legyen az internetről, és *passzív* üzemmódban működjön. A hitelesítő adatai lehetővé teszik a logikai alkalmazás számára a kapcsolat létrehozását és az FTP-fiók elérését.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, ahová el szeretné érni az FTP-fiókját. Ha egy FTP-triggert szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). FTP-művelet használatához indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="connect-to-ftp"></a>Kapcsolódás FTP-hez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be a `ftp` szűrőt. Az **Eseményindítók** listából válassza ki a kívánt eseményindítót.

   -vagy-

   Meglévő logikai alkalmazások esetében az utolsó lépésben, amelyhez műveletet szeretne hozzáadni, válassza az **új lépés**, majd a **művelet hozzáadása** lehetőséget. A keresőmezőbe írja be `ftp` szűrőként a kifejezést. A **műveletek** listából válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. Adja meg a kapcsolatok adatait, és válassza a **Létrehozás** lehetőséget.

1. Adja meg a kiválasztott trigger vagy művelet adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>FTP-trigger hozzáadása

A **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)** az trigger egy logikai alkalmazás-munkafolyamatot indít el, ha az trigger észleli, hogy egy fájlt hozzáadnak vagy módosítanak egy FTP-kiszolgálón. Hozzáadhat például egy olyan feltételt, amely ellenőrzi a fájl tartalmát, és eldönti, hogy szeretné-e lekérni a tartalmat, attól függően, hogy az adott tartalom megfelel-e a megadott feltételnek. Végül hozzáadhat egy olyan műveletet, amely beolvassa a fájl tartalmát, és az SFTP-kiszolgáló egy másik mappájába helyezi a tartalmat.

Ezzel a triggerrel például megfigyelheti az új, az ügyfelek rendeléseit leíró fájlok FTP-mappáját. Ezután használhat egy FTP-műveletet, például a **fájl metaadatainak beolvasása** lehetőséget az új fájl tulajdonságainak beszerzéséhez, majd a **fájl tartalmának** beolvasása paranccsal lekérheti az adott fájl tartalmát, így további feldolgozást hajthat végre, és a rendelést egy Orders adatbázisban tárolhatja.

Íme egy példa, amely bemutatja, hogyan használható a **fájl hozzáadása vagy módosítása (csak tulajdonságok)** trigger.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be a `ftp` szűrőt. Az eseményindítók listájában válassza ki ezt az eseményindítót: **Ha egy iktatott vagy módosított (csak tulajdonságok)**

   ![FTP-trigger keresése és kiválasztása](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Adja meg a kapcsolathoz szükséges adatokat, majd válassza a **Létrehozás** lehetőséget.

   Alapértelmezés szerint ez az összekötő szöveges formátumban továbbítja a fájlokat. Ha bináris formátumban kívánja átvinni a fájlokat, például ha a kódolást használja, válassza a **bináris átvitel** lehetőséget.

   ![FTP-kiszolgálóhoz való kapcsolódás létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. A **mappa** mezőben válassza a mappa ikont, hogy megjelenjen a lista. Az új vagy szerkesztett fájlokhoz figyelni kívánt mappa megkereséséhez válassza a derékszög nyilat ( **>** ), tallózással keresse meg a mappát, majd válassza ki a mappát.

   ![A figyelni kívánt mappa megkeresése és kiválasztása](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   A kiválasztott mappa a **mappa** mezőben jelenik meg.

   ![A kiválasztott mappa megjelenik a "mappa" tulajdonságban.](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

Most, hogy a logikai alkalmazás rendelkezik triggerrel, adja hozzá a futtatni kívánt műveleteket, amikor a logikai alkalmazás új vagy szerkesztett fájlt talál. Ebben a példában hozzáadhat egy olyan FTP-műveletet, amely az új vagy frissített tartalmat kéri le.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>FTP-művelet hozzáadása

A **fájl-metaadatok lekérése művelet beolvassa** az FTP-kiszolgálón található fájl tulajdonságait, a fájl letöltése művelet pedig az FTP-kiszolgálón lévő fájl információi alapján **beolvassa** a fájl tartalmát. Hozzáadhat például egy triggert az előző példához, és ezekkel a műveletekkel lekérheti a fájl tartalmát a fájl hozzáadása vagy szerkesztése után.

1. Az trigger vagy bármely egyéb művelet alatt válassza az **új lépés** lehetőséget.

1. A keresőmezőbe írja be `ftp` szűrőként a kifejezést. A műveletek listában válassza a következő műveletet: **fájl metaadatainak beolvasása**

   ![Válassza a "fájl metaadatainak beolvasása" műveletet.](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Ha már rendelkezik kapcsolattal az FTP-kiszolgálóval és-fiókkal, folytassa a következő lépéssel. Ellenkező esetben adja meg a kapcsolathoz szükséges adatokat, majd válassza a **Létrehozás** lehetőséget.

   ![FTP-kiszolgáló kapcsolat létrehozása](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Miután megjelenik a **fájl metaadatainak beolvasása** művelet, kattintson a **fájl** szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája. Mostantól kiválaszthatja az előző lépésekből származó kimenetek tulajdonságait. A dinamikus tartalom lista **fájl metaadatainak beolvasása** területén válassza ki a **fájlok azonosítója** tulajdonságot, amely arra a gyűjteményre hivatkozik, ahol a fájl hozzá lett adva vagy frissítve lett.

   ![A "fájlok azonosítójának listázása" tulajdonság megkeresése és kiválasztása](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   A **fájlok azonosító** tulajdonsága most megjelenik a **fájl** mezőben.

   ![A "Files id" tulajdonság kiválasztása](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Most adja hozzá ezt az FTP-műveletet: **fájl tartalmának beolvasása**

   ![A "fájl tartalmának beolvasása" művelet megkeresése és kiválasztása](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Ha megjelenik a **fájl beolvasása** művelet, kattintson a **fájl** mezőre, hogy megjelenjen a dinamikus tartalmak listája. Mostantól kiválaszthatja az előző lépésekből származó kimenetek tulajdonságait. A dinamikus tartalom lista **fájl metaadatainak beolvasása** területén válassza ki az **ID** tulajdonságot, amely a hozzáadott vagy frissített fájlra hivatkozik.

   ![Az "id" tulajdonság megkeresése és kiválasztása](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   Az **ID** tulajdonság most megjelenik a **fájl** mezőben.

   ![A kiválasztott "id" tulajdonság](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Mentse a logikai alkalmazást.

## <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzését, hogy a munkafolyamat visszaadja-e a várt tartalmat, adjon hozzá egy másik műveletet, amely elküldi a tartalmat a feltöltött vagy frissített fájlból.

1. A **fájl tartalmának beolvasása** művelet alatt adjon hozzá egy műveletet, amely képes elküldeni a fájl tartalmát. Ez a példa hozzáadja az **E-mail küldése** műveletet az Office 365 Outlookhoz.

   ![Művelet hozzáadása e-mail küldéséhez](./media/connectors-create-api-ftp/select-send-email-action.png)

1. A művelet megjelenése után adja meg az adatokat, és adja meg a tesztelni kívánt tulajdonságokat. Adja meg például a **fájl tartalma** tulajdonságot, amely a dinamikus tartalmak listájában jelenik meg, miután kiválasztotta a **továbbiak** lehetőséget a **fájl beolvasása** szakaszban.

   ![Az e-mail művelettel kapcsolatos információk megadása](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Mentse a logikai alkalmazást. A logikai alkalmazás futtatásához és elindításához kattintson az eszköztáron a **Futtatás** elemre, majd adjon hozzá egy fájlt a logikai alkalmazás által jelenleg FIGYELt FTP-mappához.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/ftpconnector/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

