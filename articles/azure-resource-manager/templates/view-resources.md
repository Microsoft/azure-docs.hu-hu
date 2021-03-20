---
title: Erőforrás-tulajdonságok felderítése
description: Az erőforrás-tulajdonságok keresésének módját ismerteti.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327748"
---
# <a name="discover-resource-properties"></a>Erőforrás-tulajdonságok felderítése

A Resource Manager-sablonok létrehozása előtt meg kell ismernie, hogy milyen típusú erőforrástípusok érhetők el, és hogy milyen értékeket kell használni a sablonban. Ebből a cikkből megtudhatja, hogyan keresheti meg a sablonban szerepeltetni kívánt tulajdonságokat.

## <a name="find-resource-provider-namespaces"></a>Erőforrás-szolgáltatói névterek keresése

Az ARM-sablonban lévő erőforrások erőforrás-szolgáltatói névtérrel és erőforrás-típussal vannak meghatározva. Például a Microsoft. Storage/storageAccounts a Storage-fiók erőforrástípus teljes neve. A Microsoft. Storage a névtér. Ha még nem ismeri a használni kívánt erőforrástípusok névtereit, tekintse meg az [Azure-szolgáltatások erőforrás-szolgáltatóit](../management/azure-services-resource-providers.md)ismertető témakört.

![Resource Manager erőforrás-szolgáltató névterének leképezése](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Sablonok exportálása

A meglévő erőforrások sablon tulajdonságainak beszerzésének legegyszerűbb módja a sablon exportálása. További információ: egy- [és többerőforrásos exportálás a Azure Portal sablonba](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Resource Manager-eszközök bővítményének használata

A Visual Studio Code és a Azure Resource Manager Tools bővítmény segítségével pontosan megtekintheti, hogy az egyes erőforrástípusok milyen tulajdonságokat igényelnek. Olyan IntelliSense-és kódrészleteket biztosítanak, amelyek egyszerűbbé teszik az erőforrások definiálását a sablonban. További információ: gyors útmutató [: Azure Resource Manager sablonok létrehozása a Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource)-ban.

Az alábbi képernyőfelvételen egy Storage-fiók erőforrás kerül a sablonba:

![Resource Manager-eszközök bővítményi kódrészletek](./media/view-resources/resource-manager-tools-extension-snippets.png)

A bővítmény a konfigurációs tulajdonságok listáján is megjeleníti a beállításokat.

![Resource Manager-eszközök bővítmény konfigurálható értékei](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Sablonreferencia használata

A sablon sémájának legátfogóbb erőforrása a Azure Resource Manager sablonra mutató hivatkozás. Megtalálhatja az API-verziókat, a sablon formátumát és a tulajdonságok adatait.

1. Tallózással keresse meg [Azure Resource Manager sablon hivatkozását](/azure/templates/).
1. A bal oldali navigációs sávon válassza a **tárterület** lehetőséget, majd válassza az **összes erőforrás** lehetőséget. A minden erőforrás lap összefoglalja az erőforrástípusok és a verziók összefoglalását.

    ![sablon-hivatkozási erőforrás-verziók](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Ha ismeri az erőforrás típusát, az alábbi URL-formátummal léphet közvetlenül erre az oldalra: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Válassza ki a legújabb verziót. Javasoljuk, hogy a legújabb API-verziót használja.

    A **sablon formátuma** szakasz a Storage-fiók összes tulajdonságát listázza. az **SKU** a listán található:

    ![sablon hivatkozása Storage-fiók formátuma](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Görgessen lefelé az **SKU objektum** megjelenítéséhez a **tulajdonságértékek** szakaszban. A cikk az SKU-név engedélyezett értékeit mutatja:

    ![sablon hivatkozása a Storage-fiók SKU-értékei](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    A lap végén a gyors üzembe helyezési **sablonok** szakasz néhány olyan Azure Gyorsindítás-sablont listáz, amely tartalmazza az erőforrás típusát:

    ![sablon hivatkozása a Storage-fiókhoz – gyorsindítási sablonok](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

A sablon hivatkozása az egyes Azure-szolgáltatások dokumentációs helyeihez van csatolva.  Például a [Key Vault dokumentációs webhely](../../key-vault/general/overview.md):

![Resource Manager-sablonok referenciája Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>erőforrás-kezelő használata

Erőforrás-kezelő be van ágyazva a Azure Portalba. A metódus használata előtt szükség van egy Storage-fiókra. Ha még nem rendelkezik ilyennel, akkor a következő gombra kattintva hozzon létre egyet:

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A keresőmezőbe írja be az **erőforrás-kezelő** kifejezést, majd válassza a **erőforrás-kezelő** lehetőséget.

    ![A képernyőképen a Azure Portal erőforrás-kezelő keresését láthatja.](./media/view-resources/azure-portal-resource-explorer.png)

1. A bal oldalon bontsa ki az **előfizetések** elemet, majd bontsa ki az Azure-előfizetését. A Storage-fiókot a **szolgáltatók** vagy a **ResourceGroups** között találja.

    ![Azure Portal erőforrás-kezelő](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Szolgáltatók**: bontsa ki a **szolgáltatók**  ->  **Microsoft. Storage**  ->  **storageAccounts**, majd válassza ki a Storage-fiókját.
    - **ResourceGroups**: válassza ki azt az erőforráscsoportot, amely tartalmazza a Storage-fiókot, válassza az **erőforrások** lehetőséget, majd válassza ki a Storage-fiókot.

    A jobb oldalon a meglévő Storage-fiók SKU-konfigurációja a következőhöz hasonló:

    ![Azure Portal erőforrás-kezelő Storage-fiók SKU](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Resources.azure.com használata

A Resources.azure.com egy nyilvános webhely, amelyet bárki elérhet egy Azure-előfizetéssel. Előzetes verzióban érhető el.  Ehelyett érdemes [erőforrás-kezelő](#use-resource-explorer) használni. Ez az eszköz a következő funkciókat biztosítja:

- Fedezze fel az Azure Resource Management API-kat.
- Az API dokumentációjának és a séma adatainak beolvasása.
- Az API-hívásokat közvetlenül a saját előfizetésében teheti meg.

Annak bemutatásához, hogyan kérhető le a séma adatai az eszköz használatával, szükség van egy Storage-fiókra. Ha még nem rendelkezik ilyennel, akkor a következő gombra kattintva hozzon létre egyet:

[![Üzembe helyezés az Azure-ban](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Tallózással keresse meg a [Resources.Azure.com](https://resources.azure.com/). Néhány percet vesz igénybe, hogy az eszköz népszerű legyen a bal oldali panelen.
1. Válassza az **előfizetések** lehetőséget.

    ![resource.azure.com API-hozzárendelés](./media/view-resources/resources-azure-com-api-mapping.png)

    A bal oldali csomópont megfelel az API-hívásnak a jobb oldalon. Az API-hívást a **Get (Letöltés** ) gombra kattintva teheti meg.
1. A bal oldalon bontsa ki az **előfizetések** elemet, majd bontsa ki az Azure-előfizetését. A Storage-fiókot a **szolgáltatók** vagy a **ResourceGroups** között találja.

    - **Szolgáltatók**: bontsa ki a **szolgáltatók**  ->  **Microsoft. Storage**  ->  **storageAccounts**, és keresse meg a Storage-fiókot.
    - **ResourceGroups**: válassza ki azt az erőforráscsoportot, amely tartalmazza a Storage-fiókot, majd válassza az **erőforrások** lehetőséget.

    A jobb oldalon a meglévő Storage-fiók SKU-konfigurációja a következőhöz hasonló:

    ![Azure Portal erőforrás-kezelő Storage-fiók SKU](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan keresheti meg a sablon sémájának adatait. A Resource Manager-sablonok létrehozásával kapcsolatos további tudnivalókért tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](./template-syntax.md)ismertető témakört.
