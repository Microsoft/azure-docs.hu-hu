---
title: Oktatóanyag – Az API verzióinak közzététele az Azure API Management
description: Az oktatóanyag lépéseit követve megtudhatja, hogyan tehet közzé több API-verziót a API Management.
author: vladvino
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: 5930979258372e4269c4d453ffbc5e0d46258088
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483609"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Oktatóanyag: Az API több verziójának közzététele 

Vannak olyankor, amikor nem praktikus, hogy az API összes hívója pontosan ugyanazt a verziót használja. Ha a hívók egy újabb verzióra szeretnék frissíteni, könnyen érthető megközelítést szeretnék. Ahogy az oktatóanyagban is látható,  az Azure-ban több verzió is API Management. 

Háttér-adatokért [lásd: Változatok & változatok.](https://azure.microsoft.com/blog/versions-revisions/)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új verzió hozzáadása meglévő API-hoz
> * Verzióséma kiválasztása
> * A verzió hozzáadása egy termékhez
> * A fejlesztői portál tallózása a verzió megtekintéséhez

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="A Azure Portal":::

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="add-a-new-version"></a>Új verzió hozzáadása

1. A [Azure Portal](https://portal.azure.com)nyissa meg a API Management példányát.
1. Válassza az **API-k** lehetőséget.
1. Válassza a **Demo Conference API** lehetőséget az API-k listájából. 
1. Válassza a Demo **Conference API** melletti helyi menüt (**...**).
1. Válassza **a Verzió hozzáadása lehetőséget.**

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API helyi menü – verzió hozzáadása":::


> [!TIP]
> Új API létrehozásakor a verziók is engedélyezhetők. Az **API hozzáadása képernyőn** válassza az **API verziószámozása?** lehetőséget.

## <a name="choose-a-versioning-scheme"></a>Verziókezelési séma kiválasztása

Az Azure API Management kiválaszthatja, hogy *a* hívók hogyan határozzák meg az API verzióját egy verziószámozási séma kiválasztásával: **elérési út, fejléc** vagy **lekérdezési sztring.** A következő példában *az elérési utat* használjuk verziószámozási sémaként.

Adja meg az alábbi táblázatban található értékeket. Ezután válassza **a Létrehozás** lehetőséget a verzió létrehozásához.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Verzió hozzáadása ablak":::



|Beállítás   |Érték  |Leírás  |
|---------|---------|---------|
|**Név**     |  *demo-conference-api-v1*       |  Egyedi név a API Management példányban.<br/><br/>Mivel a verzió valójában egy api változatán alapuló új [API,](api-management-get-started-revise-api.md)ez a beállítás az új API neve.   |
|**Verziószámozási séma**     |  **Elérési út**       |  A hívók által megadott API-verzió.     |
|**Verzióidentiferencia**     |  *v1*       |  A verzió sémaspecifikus jelzője. A **Path (Elérési** út) mezőben az API URL-címének elérési útja utótagja. <br/><br/> Ha **a Fejléc** vagy a **Lekérdezési** sztring van kiválasztva, adjon meg egy további értéket: a fejléc nevét vagy a lekérdezési sztring paraméterét.<br/><br/> Megjelenik egy használati példa.        |
|**Termékek**     |  **Korlátlan**       |  Szükség esetén egy vagy több termék, amelyekhez az API-verzió társítva van. Az API közzétételéhez társítania kell azt egy termékkel. A verziót [később is hozzáadhatja egy termékhez.](#add-the-version-to-a-product)      |

A verzió létrehozása után megjelenik az **API-lista Demo Conference API-ja** alatt. Most két API-t lát: **az eredeti**, és **a v1 API-t.**

![Az API alatt listázott verziók az Azure Portalon](media/api-management-getstarted-publish-versions/version-list.png)

Most már szerkesztheti és konfigurálhatja a **v1-et az** eredetitől különálló **API-ként.** Az egyik verzió módosítása nem érinti a másikat.

> [!Note]
> Ha egy verziót ad hozzá egy nem verziószámozású API-hoz, a rendszer automatikusan létrehoz egy **Eredetit** is. Ez a verzió az alapértelmezett URL-címen válaszol. Az Eredeti verzió létrehozása biztosítja, hogy a meglévő hívók ne hibásodnak meg a verzió hozzáadásának folyamata során. Ha olyan új API-t hoz létre, amelynél a verziók engedélyezve vannak az indításkor, a rendszer nem hoz létre eredetit.

## <a name="add-the-version-to-a-product"></a>A verzió hozzáadása egy termékhez

Ahhoz, hogy a hívók láthassák az új verziót, hozzá kell adni azt egy *termékhez*. Ha még nem adott hozzá verziót egy termékhez, bármikor hozzáadhatja egy termékhez.

Például a verzió Korlátlan termékhez való **hozzáadásához:**
1. A Azure Portal nyissa meg a API Management példányát.
1. Válassza **a Termékek Korlátlan**  >    >  **API-k**  >  **+ Hozzáadás lehetőséget.**
1. Válassza **a Demo Conference API** **1-es verzióját.**
1. Kattintson a **Kiválasztás** elemre.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Verzió hozzáadása termékhez":::

## <a name="use-version-sets"></a>Verziókészletek használata

Amikor több verziót hoz létre, a Azure Portal létrehoz egy verziókészletet, amely egyetlen logikai API verziókészletét jelöli. Válassza ki egy olyan API nevét, amely több verzióval rendelkezik. A Azure Portal megjeleníti a **Verziókészletet.** A virtuális készlet  **nevét és** leírását testreszabhatja.

A verziókészleteket közvetlenül is használhatja az Azure CLI használatával:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Az összes verziókészletet az [az apim api versionset list paranccsal láthatja:](/cli/azure/apim/api/versionset#az_apim_api_versionset_list)

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Amikor a Azure Portal létrehoz egy verziókészletet, egy alfanumerikus nevet rendel hozzá, amely a lista **Name** (Név) oszlopában jelenik meg. Használja ezt a nevet más Azure CLI-parancsokban.

A verziókészlettel kapcsolatos részletekért futtassa az [az apim api versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) parancsot:

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

A verziókészletekkel kapcsolatos további információkért lásd: [Versions in Azure API Management](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>A fejlesztői portál tallózása a verzió megtekintéséhez

Ha kipróbálta a fejlesztői [portált,](api-management-howto-developer-portal-customize.md)ott láthatja az API-verziókat.

1. A felső menüben kattintson a **Fejlesztői portál** elemre.
2. Kattintson az **API-k** elemre, majd válassza a **Demo Conference API** lehetőséget.
3. Az API neve mellett egy legördülő menünek kell lennie, amely több verziót is használ.
4. Válassza a **v1** lehetőséget.
5. Figyelje meg a lista első műveletének **Kérés URL-címe** értékét. Azt mutatja, hogy az API URL-címe tartalmazza a **v1** tagot.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Új verzió hozzáadása meglévő API-hoz
> * Verzióséma kiválasztása 
> * A verzió hozzáadása egy termékhez
> * A fejlesztői portál tallózása a verzió megtekintéséhez

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [A fejlesztői portál oldalstílusának testreszabása](api-management-howto-developer-portal-customize.md)
