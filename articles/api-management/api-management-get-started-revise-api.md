---
title: Oktatóanyag – a API Management változatok használata a nem feltörhető API-változások biztonságos elvégzéséhez
titleSuffix: Azure API Management
description: Az oktatóanyag lépéseiből megtudhatja, hogyan hajthat végre nem kompatibilitástörő változásokat változatok használatával az API Managementben.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: acb121bb00df481c926ebed9594bf0fe1b9b17ed
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546635"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Oktatóanyag: a nem betörést jelentő API-k biztonságos módosítására szolgáló változatok használata
Amikor az API készen áll, és a fejlesztők elkezdik a használatát, előbb-utóbb módosításokat kell végeznie az API-n, miközben nem akadályozza az API hívóit. Emellett az is hasznos, ha a fejlesztők értesülnek az elvégzett módosításokról. 

Az Azure API Managementban az *API* -k nem törhető módosításaival végezheti el a módosításokat, így biztonságosan modellezheti és tesztelheti a változtatásokat. Ha elkészült, megteheti az aktuális változatot, és lecserélheti a jelenlegi API-t. 

A háttérben lásd: [verziók & változatok](https://azure.microsoft.com/blog/versions-revisions/) és API- [verziószámozás az Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/)használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új változat hozzáadása
> * Nem kompatibilitástörő változások végrehajtása a változaton
> * Változat aktuálissá tétele és módosításinapló-bejegyzés hozzáadása
> * A fejlesztői portál tallózása a módosítások és a módosítási napló megtekintéséhez

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="API-változatok a Azure Portal":::

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="add-a-new-revision"></a>Új változat hozzáadása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és navigáljon a API Management-példányhoz.
1. Válassza az **API-k** lehetőséget.
2. Válassza ki a **bemutató konferencia API** -t az API-listából (vagy egy másik API-t, amelyhez a változatokat hozzá kívánja adni).
3. Válassza a **változatok** fület.
4. Válassza a **+ változat hozzáadása** elemet.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="API-változat hozzáadása":::

    > [!TIP]
    > Az API helyi menüjében (**...**) is kiválaszthatja a **változat hozzáadása** elemet.

5. Adja meg az új változat leírását, amely alapján emlékezni fog a változat funkciójára.
6. Válassza a **Létrehozás** lehetőséget,
7. Az új változat létrehozása megtörtént.

    > [!NOTE]
    > Az eredeti API az **1. változatban** marad. A felhasználók ezt a változatot hívják egészen addig, amíg Ön nem tesz aktuálissá egy másik változatot.

## <a name="make-non-breaking-changes-to-your-revision"></a>Nem kompatibilitástörő változások végrehajtása a változaton

1. Válassza a **Demo Conference API** lehetőséget az API-k listájából.
1. Kattintson a képernyő felső részén található **Tervezés** fülre.
1. Figyelje meg, hogy a (közvetlenül a Tervezés lap fölött található) **változatválasztóban** a **2. változat** jelenik meg aktuális változatként.

    > [!TIP]
    > A változatválasztóval váltson arra a változatra, amelyen dolgozni kíván.
1. Válassza a **+ Művelet hozzáadása** lehetőséget.
1. Az új művelet legyen **POST**, a művelet Neve, Megjelenítendő neve és URL-címe pedig **test**.
1. **Mentse** az új műveletet.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Változat módosítása":::
1. Most módosította a **2. változatot**. A lap tetején található **változat-választóval** váltson vissza az **1. változatra**.
1. Figyelje meg, hogy az új művelet nem jelenik meg az **1. változatban**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Változat aktuálissá tétele és módosításinapló-bejegyzés hozzáadása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Kattintson a **Változatok** fülre az oldal felső részén található menüben.
1. Nyissa meg a **2. változat** helyi menüjét (**...**).
1. Válassza a **jelenlegivé tétele** lehetőséget.
1. Ha a módosítással kapcsolatos megjegyzéseket szeretne küldeni, jelölje be a közzététel **nyilvános módosítási naplóba** jelölőnégyzetet. Adja meg a változás leírását, amelyet a fejlesztők látnak, például: **tesztelési változatok. Új "teszt" művelet lett hozzáadva.**
1. Most már a **2. változat** az aktuális.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Változat menü a változatok ablakban":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdéséhez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Ezzel az eljárással hozhat létre és frissíthet kiadásokat.

1. Az API-azonosítók megjelenítéséhez futtassa az az [APIM API List](/cli/azure/apim/api#az_apim_api_list) parancsot:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   A következő parancsban használandó API-azonosító az `Name` érték. Az API-változat az `ApiRevision` oszlopban található.

1. A kiadás létrehozásához kiadási megjegyzéssel futtassa az az [APIM API Release Create](/cli/azure/apim/api/release#az_apim_api_release_create) parancsot:

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   A kiadott változat lesz az aktuális változat.

1. A kiadások megtekintéséhez használja az az [APIM API Release List](/cli/azure/apim/api/release#az_apim_api_release_list) parancsot:

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   A megadott megjegyzések megjelennek a changelog. Ezeket az előző parancs kimenetében tekintheti meg.

1. Kiadás létrehozásakor a `--notes` paraméter megadása nem kötelező. A jegyzeteket később is hozzáadhatja vagy módosíthatja az az [APIM API Release Update](/cli/azure/apim/api/release#az_apim_api_release_update) parancs használatával:

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Használja a `Name` kiadási azonosító oszlopban található értéket.

Az az [APIM API Release delete ](/cli/azure/apim/api/release#az_apim_api_release_delete) paranccsal eltávolíthatja az összes kiadást:

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>A fejlesztői portál tallózása a módosítások és a módosítási napló megtekintéséhez

Ha kipróbálta a [fejlesztői portált](api-management-howto-developer-portal-customize.md), tekintse át az API-módosításokat, és jelentkezzen be ide.

1. Az Azure Portalon válassza az **API-k** lehetőséget.
1. Válassza a **fejlesztői portál** lehetőséget a felső menüben.
1. A fejlesztői portálon válassza az **API**-k, majd a **bemutató konferencia API** elemet.
1. Figyelje meg, hogy az új **test** művelet már elérhető.
1. Az API neve közelében válassza a **changelog** lehetőséget.
1. Figyelje meg, hogy a módosításinapló-bejegyzés megjelenik a listában.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új változat hozzáadása
> * Nem kompatibilitástörő változások végrehajtása a változaton
> * Változat aktuálissá tétele és módosításinapló-bejegyzés hozzáadása
> * A fejlesztői portál tallózása a módosítások és a módosítási napló megtekintéséhez

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az API több verziójának közzététele](api-management-get-started-publish-versions.md)
