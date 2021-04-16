---
title: Oktatóanyag – A nem API Management API-módosítások biztonságos útjára használhatja a API Management változatok használatával
titleSuffix: Azure API Management
description: Az oktatóanyag lépéseiből megtudhatja, hogyan hajthat végre nem kompatibilitástörő változásokat változatok használatával az API Managementben.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 1d99d6f876e4896bb4321afb8dc4d8e7c3a404e7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483554"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Oktatóanyag: Változatok használata nem használ használva a nem használható API-módosítások biztonságos felülvizsgálatát
Amikor az API készen áll, és a fejlesztők elkezdik a használatát, előbb-utóbb módosításokat kell végeznie az API-n, miközben nem akadályozza az API hívóit. Emellett az is hasznos, ha a fejlesztők értesülnek az elvégzett módosításokról. 

Az Azure API Management változatok  használatával nem használ használ feltörést nem hozó API-módosításokat, így biztonságosan modellelheti és tesztelheti a módosításokat. Ha elkészült, egy változatot aktuálisra cserélhet, és lecserélheti az aktuális API-t. 

Háttér-adatokért lásd: [Változatok &](https://azure.microsoft.com/blog/versions-revisions/) api-verziók és [API-verziószámozás az Azure API Management.](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/)

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

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)és keresse meg a API Management példányát.
1. Válassza az **API-k** lehetőséget.
2. Válassza **a Demo Conference API** lehetőséget az API-k listájából (vagy egy másik API-t, amelyhez változatokat szeretne hozzáadni).
3. Válassza a **Változatok** lapot.
4. Válassza **a + Változat hozzáadása lehetőséget.**

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="API-változat hozzáadása":::

    > [!TIP]
    > Az API helyi **menüjében** (**...**) is kiválaszthatja a Változat hozzáadása lehetőséget.

5. Adja meg az új változat leírását, amely alapján emlékezni fog a változat funkciójára.
6. Válassza a **Létrehozás** lehetőséget.
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
1. Ez után a **2. változatot módosítja.** Az oldal **tetején** található változatválasztóval váltson vissza az **1. változatra.**
1. Figyelje meg, hogy az új művelet nem jelenik meg az **1. változatban**. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Változat aktuálissá tétele és módosításinapló-bejegyzés hozzáadása

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Kattintson a **Változatok** fülre az oldal felső részén található menüben.
1. Nyissa meg a **2. változat** helyi menüjét (**...**).
1. Válassza **az Aktuális legyen lehetőséget.**
1. Jelölje be **a Post to Public Change Log for this API** (Bejegyzés a nyilvános módosítási naplóban ehhez az API-hoz) jelölőnégyzetet, ha megjegyzéseket szeretne tenni a változásról. Adja meg a módosítás fejlesztői által látható leírását, például: **Változatok tesztelése. Új "teszt" művelet hozzáadva.**
1. Most már a **2. változat** az aktuális.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Változat menü a Változatok ablakban":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdése:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Ezzel az eljárással hozhat létre és frissítheti a kiadást.

1. Futtassa [az az apim api list parancsot](/cli/azure/apim/api#az_apim_api_list) az API-k listájának a listában:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   A következő parancsban használt API-azonosító az `Name` érték. Az API-változat az `ApiRevision` oszlopban található.

1. A kiadás kiadási megjegyzéssel való létrehozásához futtassa az [az apim api release create](/cli/azure/apim/api/release#az_apim_api_release_create) parancsot:

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   A kiadásban lévő változat lesz az aktuális változat.

1. A kiadásokat az [az apim api release list paranccsal láthatja:](/cli/azure/apim/api/release#az_apim_api_release_list)

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   A megadott megjegyzések megjelennek a változásnaplóban. Ezeket az előző parancs kimenetében láthatja.

1. Kiadás létrehozásakor a paraméter `--notes` megadása nem kötelező. A megjegyzéseket később is hozzáadhatja vagy módosíthatja az [az apim api release update paranccsal:](/cli/azure/apim/api/release#az_apim_api_release_update)

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Használja a kiadásazonosító `Name` oszlopában található értéket.

Bármilyen kiadást eltávolíthat az [az apim api release delete parancs futtatásával:](/cli/azure/apim/api/release#az_apim_api_release_delete)

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>A fejlesztői portál tallózása a módosítások és a módosítási napló megtekintéséhez

Ha kipróbálta a fejlesztői [portált,](api-management-howto-developer-portal-customize.md)áttekintheti az API módosításait, és ott módosíthatja a naplót.

1. Az Azure Portalon válassza az **API-k** lehetőséget.
1. A **felső menüben** válassza a Fejlesztői portál lehetőséget.
1. A fejlesztői portálon válassza az **API-k,** majd a **Demo Conference API lehetőséget.**
1. Figyelje meg, hogy az új **test** művelet már elérhető.
1. Válassza **a Changelog (Változásnapló)** lehetőséget az API neve mellett.
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
