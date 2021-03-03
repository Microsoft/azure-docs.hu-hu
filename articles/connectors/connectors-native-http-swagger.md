---
title: Kapcsolódás REST-végpontokhoz Azure Logic Apps
description: A REST-végpontok figyelése az automatizált feladatokban, folyamatokban és munkafolyamatokban Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 810aaae9634a7de8d07b6d49edd0c6c2eda96754
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730881"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>REST-végpontok hívása Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http + hencegő összekötővel automatizálhatja azokat a munkafolyamatokat, amelyek rendszeresen meghívnak bármely Rest-végpontot egy [hencegő fájlon](https://swagger.io) keresztül a logikai alkalmazások létrehozásával. A HTTP + hencegés-trigger és a művelet ugyanúgy működik, mint a [http-trigger és a művelet](connectors-native-http.md) , de a Logic app Designerben jobb élményt nyújt, ha kiteszi a hencegő fájl által leírt API-struktúrát és kimeneteket. A lekérdezési trigger megvalósításához kövesse az egyéni API-k létrehozása című témakörben leírt lekérdezési mintát, amely [más API-kat, szolgáltatásokat és rendszereket hív meg a Logic apps szolgáltatásban](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A cél REST-végpontot leíró hencegő (nem OpenAPI) fájl URL-címe

  Általában a REST-végpontnak teljesítenie kell ezt a feltételt az összekötő működéséhez:

  * A hencegő fájlt nyilvánosan elérhető HTTPS URL-címen kell tárolni.
  
  * A hencegő fájlnak tartalmaznia kell egy `operationID` , a definícióban szereplő minden művelethez. Ha nem, az összekötő csak az utolsó műveletet jeleníti meg a hencegő fájlban. 

  * A hencegő fájlnak engedélyezni kell a [több eredetű erőforrás-megosztást (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

  Ha olyan hencegő fájlra szeretne hivatkozni, amely nem található meg, vagy nem felel meg a biztonsági és az egyéb eredetű követelményeknek, [feltöltheti a hencegő fájlt egy Azure Storage-fiókban található blob-tárolóba](#host-swagger), és engedélyezheti a CORS a fájlra való hivatkozáshoz.

  A jelen témakörben szereplő példák a [Cognitive Services Face APIt](../cognitive-services/face/overview.md)használják, amelyhez [Cognitive Services fiók és hozzáférési kulcs](../cognitive-services/cognitive-services-apis-create-account.md)szükséges.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* Az a logikai alkalmazás, amelyről meg szeretné hívni a célként megadott végpontot. A HTTP + hencegő triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP + hencegés művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP + hencegő triggert használja első lépésként.

## <a name="add-an-http--swagger-trigger"></a>HTTP + hencegő trigger hozzáadása

Ez a beépített trigger HTTP-kérést küld egy REST API leíró hencegő fájl URL-címére, és egy olyan választ ad vissza, amely tartalmazza a fájl tartalmát.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A tervezőben a keresőmezőbe írja be szűrőként a "hencegés" kifejezést. Az **Eseményindítók** listából válassza ki a **http + hencegő** eseményindítót.

   ![HTTP + hencegő trigger kiválasztása](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. A **hencegő végpont URL-címe** mezőbe írja be a hencegő fájl URL-címét, majd kattintson a **Tovább gombra**.

   Ez a példa a [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)az USA nyugati régiójában található hencegő URL-címet használja:

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Képernyőfelvétel: a Logic app Designer és a "H T T P + henceg" trigger és a "hencegő végpont U R L" tulajdonsága U R L értékre van beállítva.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Ha a tervező megjeleníti a hencegő fájl által leírt műveleteket, válassza ki a használni kívánt műveletet.

   ![Képernyőkép, amely a "H T P + henceg" triggerrel rendelkező Logic app designert és a kivágási műveleteket megjelenítő listát jeleníti meg.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Adja meg az trigger paramétereinek értékeit, amelyek a kiválasztott művelettől függően változnak a végponti hívásban. Állítsa be az ismétlődést, hogy az eseményindító milyen gyakran hívja meg a végpontot.

   Ez a példa átnevezi a triggert a "HTTP + hencegő trigger: Face-Detect" kifejezésre, hogy a lépésnek legyen egy leíró neve.

   ![Képernyőfelvétel: a Logic app Designer és a "H T T P + henceg" trigger, amely megjeleníti az "Arcfelismerés" műveletet.](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

   A HTTP + hencegés számára elérhető hitelesítési típusokkal kapcsolatos további információkért lásd: [hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Folytassa a logikai alkalmazás munkafolyamatának kialakítását olyan műveletekkel, amelyek az eseményindító indításakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

## <a name="add-an-http--swagger-action"></a>HTTP + hencegés művelet hozzáadása

Ez a beépített művelet HTTP-kérést küld a REST API leíró hencegő fájl URL-címére, és egy választ ad vissza, amely tartalmazza a fájl tartalmát.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Válassza ki azt a lépést, amelyben hozzá szeretné adni a HTTP + hencegés műveletet, és válassza az **új lépés** lehetőséget.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. A tervezőben a keresőmezőbe írja be szűrőként a "hencegés" kifejezést. A **műveletek** listából válassza a **http + hencegés** műveletet.

    ![HTTP + hencegés művelet kiválasztása](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. A **hencegő végpont URL-címe** mezőbe írja be a hencegő fájl URL-címét, majd kattintson a **Tovább gombra**.

   Ez a példa a [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)az USA nyugati régiójában található hencegő URL-címet használja:

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Adja meg a hencegő végpont URL-címét](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Ha a tervező megjeleníti a hencegő fájl által leírt műveleteket, válassza ki a használni kívánt műveletet.

   ![Műveletek a hencegő fájlban](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Adja meg a műveleti paraméterek értékeit, amelyek a kiválasztott művelettől függően változnak a végponti hívásban.

   Ebben a példában nincsenek paraméterek, de a műveletet átnevezi a "HTTP + hencegés művelet: Face-IDENTIFY" kifejezésre, hogy a lépésnek legyen egy leíró neve.

   ![Művelet részletei](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

   A HTTP + hencegés számára elérhető hitelesítési típusokkal kapcsolatos további információkért lásd: [hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Üzemeltetés az Azure Storage-ban

Hivatkozhat olyan hencegő fájlra, amely nem üzemeltetett, vagy nem felel meg a biztonsági és az egyéb eredetű követelményeknek azáltal, hogy feltölti a fájlt a blob-tárolóba egy Azure Storage-fiókban, és engedélyezi a CORS. Az Azure Storage-ban az alábbi lépéseket követve hozhatja létre, állíthatja be és tárolhatja a hencegő fájlokat:

1. [Hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md).

1. Most engedélyezze a CORS a blobhoz. A Storage-fiók menüjében válassza a **CORS** lehetőséget. A **blob Service** lapon adja meg ezeket az értékeket, majd kattintson a **Mentés** gombra.

   | Tulajdonság | Érték |
   |----------|-------|
   | **Engedélyezett eredetek** | `*` |
   | **Engedélyezett metódusok** | `GET`, `HEAD`, `PUT` |
   | **Engedélyezett fejlécek** | `*` |
   | **Közzétett fejlécek** | `*` |
   | **Maximális életkor** (másodpercben) | `200` |
   |||

   Bár ez a példa a [Azure Portal](https://portal.azure.com)használja, használhat egy eszközt, például [Azure Storage Explorert](https://storageexplorer.com/), vagy automatikusan konfigurálhatja ezt a beállítást a [PowerShell-parancsfájl](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)használatával.

1. [Hozzon létre egy BLOB-tárolót](../storage/blobs/storage-quickstart-blobs-portal.md). A tároló **Áttekintés** paneljén válassza a **hozzáférési szint módosítása** lehetőséget. A **nyilvános hozzáférési szint** listából válassza a **blob (névtelen olvasási hozzáférés csak blobokhoz)** lehetőséget, majd kattintson **az OK gombra**.

1. [Töltse fel a hencegő fájlt a blob-tárolóba](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), vagy a [Azure Portalon](https://portal.azure.com) vagy [Azure Storage Exploreron](https://storageexplorer.com/)keresztül.

1. A blob-tárolóban található fájlra való hivatkozáshoz szerezze be az ezt a formátumot követő HTTPS URL-címet, amely megkülönbözteti a kis-és nagybetűket, a Azure Storage Explorer:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Összekötő-referencia

Itt talál további információt a HTTP + hencegő triggerből vagy műveletből származó kimenetekről. A HTTP + hencegés hívása ezt az információt adja vissza:

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| fejlécek | object | A kérelemben szereplő fejlécek |
| body (Törzs) | object | JSON-objektum | A kérelem szövegtörzsét tartalmazó objektum |
| állapotkód | int | A kérelemben szereplő állapotkód |
|||

| Állapotkód | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Hibás kérelem |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
