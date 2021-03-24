---
title: Oktatóanyag – az API verzióinak közzététele az Azure API Management használatával
description: Az oktatóanyag lépéseivel megtudhatja, hogyan tehet közzé több API-verziót API Managementban.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: apimpm
ms.openlocfilehash: dc3d3b4658bd9a26363b6d41377d448059e30c0f
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889671"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Oktatóanyag: az API több verziójának közzététele 

Vannak olyan időpontok, amikor nem praktikus, hogy az API-hívók pontosan ugyanazt a verziót használják. Ha a hívók egy későbbi verzióra szeretnének frissíteni, egy könnyen értelmezhető megközelítést szeretnének. Ahogy az oktatóanyagban is látható, több *verzió* is elérhető az Azure API Managementban. 

A háttérben lásd: [verziók & változatok](https://azure.microsoft.com/blog/versions-revisions/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Új verzió hozzáadása meglévő API-hoz
> * Verzióséma kiválasztása
> * A verzió hozzáadása egy termékhez
> * A fejlesztői portál tallózása a verzió megtekintéséhez

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="A Azure Portalban látható verzió":::

## <a name="prerequisites"></a>Előfeltételek

+ Az [Azure API Management terminológiájának](api-management-terminology.md) ismerete.
+ Tekintse át a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md).
+ Végezze el a következő oktatóanyagot is: [Az első API importálása és közzététele](import-and-publish.md).

## <a name="add-a-new-version"></a>Új verzió hozzáadása

1. A [Azure Portal](https://portal.azure.com)navigáljon a API Management-példányhoz.
1. Válassza az **API-k** lehetőséget.
1. Válassza a **Demo Conference API** lehetőséget az API-k listájából. 
1. Válassza a **bemutató konferencia API** melletti helyi menüt (**..**.).
1. Válassza a **verzió hozzáadása** lehetőséget.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API-környezet menü – verzió hozzáadása":::


> [!TIP]
> Új API létrehozásakor a verziók is engedélyezhetők. Az **API hozzáadása** képernyőn válassza az **API verziója lehetőséget?**.

## <a name="choose-a-versioning-scheme"></a>Verziókezelési séma kiválasztása

Az Azure API Managementon válassza ki, hogy a hívók hogyan adja meg az API-verziót a *verziószámozási séma* kiválasztásával: **elérési út, fejléc** vagy **lekérdezési karakterlánc**. A következő példában a rendszer az *elérési utat* használja verziószámozási sémaként.

Adja meg az értékeket az alábbi táblázatból. Ezután válassza a **Létrehozás** lehetőséget a verzió létrehozásához.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Verzió hozzáadása ablak":::



|Beállítás   |Érték  |Leírás  |
|---------|---------|---------|
|**Név**     |  *bemutató-Conference-API-v1*       |  Egyedi név a API Management-példányban.<br/><br/>Mivel a verzió valójában egy API-változaton alapuló új [API, ez](api-management-get-started-revise-api.md)a beállítás az új API neve.   |
|**Verziószámozási séma**     |  **Elérési út**       |  A hívók határozzák meg az API verzióját.     |
|**Termékazonosító verziója**     |  *v1*       |  A verzió séma-specifikus jelzője. Az **elérési út** esetében az API URL-címének utótagja. <br/><br/> Ha a **fejléc** vagy a **lekérdezési karakterlánc** van kiválasztva, adjon meg egy további értéket: a fejléc vagy a lekérdezési karakterlánc paraméter nevét.<br/><br/> Megjelenik egy használati példa.        |
|**Termékek**     |  **Korlátlan**       |  Szükség esetén egy vagy több olyan termékhez, amelyhez az API verziója társítva van. Az API közzétételéhez társítania kell azt egy termékkel. [A verziót később is hozzáadhatja egy termékhez](#add-the-version-to-a-product) .      |

A verzió létrehozása után már megjelenik a **bemutató konferencia API** alatt az API-listán. Ekkor két API jelenik meg: **eredeti** és **v1**.

![Az API alatt listázott verziók az Azure Portalon](media/api-management-getstarted-publish-versions/version-list.png)

Most már szerkesztheti és konfigurálhatja a **v1** -et az **EREDETItől** eltérő API-k használatával. Az egyik verzió módosítása nem érinti a másikat.

> [!Note]
> Ha egy verziót ad hozzá egy nem verziójú API-hoz, az **eredeti** is automatikusan létrejön. Ez a verzió válaszol az alapértelmezett URL-címre. Az eredeti verzió létrehozása biztosítja, hogy a meglévő hívók ne legyenek megszakítva a verziók hozzáadásának folyamatával. Ha olyan új API-t hoz létre, amelynek verziói engedélyezve vannak az indításkor, az eredeti nincs létrehozva.

## <a name="add-the-version-to-a-product"></a>A verzió hozzáadása egy termékhez

Ahhoz, hogy a hívók láthassák az új verziót, hozzá kell adni azt egy *termékhez*. Ha még nem adta hozzá a verziót egy termékhez, bármikor hozzáadhatja egy termékhez.

Ha például a verziót szeretné hozzáadni a **korlátlan** termékhez:
1. A Azure Portal navigáljon a API Management-példányhoz.
1. Válassza a **termékek**  >  **korlátlan**  >  **API**  >  **+ Hozzáadás** lehetőséget.
1. Válassza a **bemutató konferencia API**, **v1** verzió elemet.
1. Kattintson a **Kiválasztás** elemre.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Verzió hozzáadása a termékhez":::

## <a name="use-version-sets"></a>Verzió-készletek használata

Ha több verziót hoz létre, a Azure Portal egy olyan *verziót* hoz létre, amely egyetlen logikai API-hoz tartozó verziók egy készletét jelöli. Válassza ki azt az API-t, amelynek több verziója van. A Azure Portal megjeleníti a **verziószámát**. Testre szabhatja egy virtuális készlet **nevét** és **leírását** .

Az Azure CLI használatával közvetlenül is használhatja a verziószámokat:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Az összes verzió megjelenítéséhez futtassa az az [APIM API versionset List](/cli/azure/apim/api/versionset#az_apim_api_versionset_list) parancsot:

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Ha a Azure Portal létrehoz egy beállított verziót, az egy alfanumerikus nevet rendel hozzá, amely megjelenik a lista Name ( **név** ) oszlopában. Használja ezt a nevet más Azure CLI-parancsokban.

Ha meg szeretné tekinteni a verziószám részleteit, futtassa az az [APIM API versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) parancsot:

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

További információ a [verziókról: az Azure API Management verziói](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>A fejlesztői portál tallózása a verzió megtekintéséhez

Ha kipróbálta a [fejlesztői portált](api-management-howto-developer-portal-customize.md), Itt láthatja az API-verziókat.

1. A felső menüben kattintson a **Fejlesztői portál** elemre.
2. Kattintson az **API-k** elemre, majd válassza a **Demo Conference API** lehetőséget.
3. Az API neve mellett több verziót tartalmazó legördülő listát kell látnia.
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
