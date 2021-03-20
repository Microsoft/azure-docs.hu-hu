---
title: Oktatóanyag – API-k importálása és kezelése – Azure API Management és Visual Studio Code | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan használható az Azure API Management bővítmény a Visual Studio Code-hoz az API-k importálásához, teszteléséhez és kezeléséhez.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0dea8e43d5f09b84c5795bc257cf3331ad919fcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649546"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Oktatóanyag: az API-k importálásához és kezeléséhez használja a Visual Studio Code API Management bővítményét

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Visual Studio Code-hoz készült API Management-bővítményt a API Management gyakori műveleteihez. A jól ismert Visual Studio Code-környezet használatával API-kat importálhat, frissíthet, tesztelheti és kezelheti.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * API importálása API Managementba
> * Az API szerkesztése
> * API Management házirendek alkalmazása
> * Az API tesztelése


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API API Management-bővítményben":::

A további API Management funkciók bevezetését a [Azure Portal](import-and-publish.md)használatával foglalkozó API Management oktatóanyagokban találhatja meg.

## <a name="prerequisites"></a>Előfeltételek
- Az [Azure API Management terminológiájának](api-management-terminology.md) ismertetése
- Győződjön meg arról, hogy telepítette a [Visual Studio Code](https://code.visualstudio.com/) -ot és a legújabb [Azure API Management-bővítményt a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) -hoz
- [API Management-példány létrehozása](vscode-create-service-instance.md)

## <a name="import-an-api"></a>API importálása

Az alábbi példa JSON formátumú OpenAPI-specifikációt importál API Managementba. A Microsoft biztosítja az ebben a példában használt háttér-API-t, és az Azure-on üzemelteti azt `https://conferenceapi.azurewebsites.net?format=json` .

1. A Visual Studio Code-ban válassza ki az Azure ikont a tevékenység sávjából.
1. Az Explorer ablaktáblán bontsa ki a létrehozott API Management példányt.
1. Kattintson a jobb gombbal az **API**-k elemre, majd válassza **az Importálás a OpenAPI hivatkozásból** lehetőséget. 
1. Ha a rendszer kéri, adja meg a következő értékeket:
    1. **OpenAPI-hivatkozás** JSON formátumú tartalomhoz. Ehhez a példához: *https://conferenceapi.azurewebsites.net?format=json* .
    Ez az URL-cím az a szolgáltatás, amely megvalósítja a példa API-t. API Management továbbítja a kéréseket erre a címnek.
    1. Egy **API-név**, például a *bemutató-Conference-API*, amely egyedi a API Management-példányban. Ez a név csak betűket, számokat és kötőjeleket tartalmazhat. Az első és az utolsó karakternek alfanumerikusnak kell lennie. Ez a név az API meghívásához használt elérési úton található.

Az API sikeres importálása után megjelenik az Explorer ablaktáblán, és az elérhető API-műveletek az **Operations** csomópont alatt jelennek meg.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Importált API az Explorer ablaktáblán":::

## <a name="edit-the-api"></a>Az API szerkesztése

Az API-t szerkesztheti a Visual Studio Code-ban. Szerkessze például az API Resource Manager JSON-leírását a szerkesztő ablakban az API eléréséhez használt **http** -protokoll eltávolításához. Ezután válassza a **fájl**  >  **Mentés** lehetőséget.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="JSON-Leírás szerkesztése":::

A OpenAPI formátumának szerkesztéséhez kattintson a jobb gombbal az API nevére az Explorer ablaktáblán, és válassza a **OpenAPI szerkesztése** lehetőséget. Végezze el a módosításokat, majd válassza a **fájl**  >  **Mentés** lehetőséget.

## <a name="apply-policies-to-the-api"></a>Házirendek alkalmazása az API-ra 

API Management az API-khoz konfigurálható [házirendeket](api-management-policies.md) biztosít. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A szabályzatok globálisak lehetnek, amelyek a API Management-példány összes API-ra érvényesek, vagy hatókörük egy adott API-vagy API-műveletre is vonatkozhat.

Ez a szakasz bemutatja, hogyan alkalmazhat néhány gyakori kimenő házirendet az API-ra, amely átalakítja az API-választ. Az ebben a példában szereplő szabályzatok megváltoztatja a válasz fejléceit, és elrejti az eredeti háttérbeli URL-címeket, amelyek megjelennek a válasz törzsében.

1. Az Explorer ablaktáblán válassza a **házirend** elemet az importált *bemutató-konferencia-API* alatt. A házirend-fájl a szerkesztő ablakban nyílik meg. Ez a fájl az API összes műveletére vonatkozó házirendeket konfigurál. 

1. Frissítse a fájlt a következő tartalommal a `<outbound>` elemben:
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * Az első `set-header` házirend egy egyéni válasz fejlécét adja meg bemutató célra.
    * A második `set-header` szabályzat törli az **X-alapú-by** fejlécet, ha létezik. Ez a fejléc felfedi az API-háttérben használt alkalmazás-keretrendszert, a közzétevők pedig gyakran eltávolítják azt.
    * A `redirect-content-urls` házirend a válasz törzsében lévő, a API Management átjárón keresztül az egyenértékű hivatkozásokra mutató hivatkozásokat.
    
1. Mentse a fájlt. Ha a rendszer kéri, válassza a **feltöltés** lehetőséget a fájl felhőbe való feltöltéséhez.

## <a name="test-the-api"></a>Az API tesztelése

### <a name="get-the-subscription-key"></a>Az előfizetési kulcs beszerzése

Az importált API és az alkalmazott szabályzatok teszteléséhez szüksége lesz egy előfizetési kulcsra a API Management-példányhoz.

1. Az Explorer ablaktáblán kattintson a jobb gombbal a API Management példányának nevére.
1. Válassza az **előfizetési kulcs másolása** lehetőséget.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Előfizetés kulcsának másolása":::

### <a name="test-an-api-operation"></a>API-művelet tesztelése

1. Az Explorer ablaktáblán bontsa ki az importált *demo-Conference-API* alatt lévő **Operations** csomópontot.
1. Válasszon egy műveletet, például *GetSpeakers*.
1. A szerkesztő ablakban a **OCP-APIM-előfizetés-Key** elem mellett cserélje le `{{SubscriptionKey}}` a elemet a másolt előfizetési kulcsra.
1. Válassza a **Kérés elküldése** elemet. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="API-kérelem küldése a Visual Studio Code-ból":::

Ha a kérelem sikeres, a háttérrendszer **200 OK** és néhány adattal válaszol.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API-tesztelési művelet":::

Figyelje meg a válaszban a következő adatokat:
* A rendszer hozzáadja az **Egyéni** fejlécet a válaszhoz.
* Az **X-alapú-by** fejléc nem jelenik meg a válaszban.
* Az API-háttér URL-címei átirányítva lesznek a API Management átjáróra, ebben az esetben `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>Az API-művelet nyomon követése

Az API-művelet hibakereséséhez segítséget nyújtó részletes nyomkövetési információkat a **OCP-APIM-Trace-Location** mellett megjelenő hivatkozásra kattintva tekintheti meg. 

Az adott helyen található JSON-fájl bejövő, háttérbeli és kimenő nyomkövetési adatokat tartalmaz, így meghatározhatja, hogy a kérést követően milyen problémák történnek.

> [!TIP]
> Az API-műveletek tesztelésekor a API Management bővítmény lehetővé teszi a választható [szabályzatok hibakeresését](api-management-debug-policies.md) (a fejlesztői szolgáltatási szinten érhető el).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el a API Management példányt a [API Management szolgáltatás](get-started-create-service-instance.md#clean-up-resources) és az erőforráscsoport törléséhez kattintson a jobb gombbal, és válassza a **Megnyitás a portálon** lehetőséget.

Másik lehetőségként kiválaszthatja a **delete API Management** elemet a API Management példány törléséhez (ez a művelet nem törli az erőforráscsoportot).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="API Management példány törlése a VS Code-ból":::

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag a Visual Studio Code-hoz készült API Management bővítmény számos funkcióját ismerteti, amelyeket az API-k importálására és kezelésére használhat. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * API importálása API Managementba
> * Az API szerkesztése
> * API Management házirendek alkalmazása
> * Az API tesztelése

A API Management bővítmény további funkciókat biztosít az API-kkal való együttműködéshez. Például [hibakeresési házirendek](api-management-debug-policies.md) (a fejlesztői szolgáltatási szinten érhetők el), illetve [elnevezett értékek](api-management-howto-properties.md)létrehozása és kezelése.