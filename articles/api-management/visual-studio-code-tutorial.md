---
title: Oktatóanyag – API-k importálása és kezelése – Azure API Management és Visual Studio Code | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan importálhat, tesztelhet és kezelhet API-kat az Azure API Management Code-hoz Visual Studio Code-hoz.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0090d981e93cee12f2feaaf7d2c12f341564f6ec
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482330"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Oktatóanyag: API-API Management importálása és kezelése Visual Studio Code-hoz használható Visual Studio bővítmény

Ez az oktatóanyag bemutatja, hogyan használhatja a API Management Code-hoz Visual Studio bővítményt a API Management. A kódkörnyezet ismerős Visual Studio API-kat importálhat, frissíthet, tesztelhet és kezelhet.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * API importálása API Management
> * Az API szerkesztése
> * Alkalmazás API Management szabályzatok
> * Az API tesztelése


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API a API Management bővítményben":::

A további API Management a API Management oktatóanyagaiból [Azure Portal be.](import-and-publish.md)

## <a name="prerequisites"></a>Előfeltételek
- Az [Azure API Management terminológiája](api-management-terminology.md)
- Győződjön meg arról, [hogy telepítette Visual Studio](https://code.visualstudio.com/) Code-et és a legújabb [Azure API Management-bővítményt a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)
- [API Management-példány létrehozása](vscode-create-service-instance.md)

## <a name="import-an-api"></a>API importálása

Az alábbi példa JSON formátumban importál egy OpenAPI-specifikációt a API Management. A Microsoft biztosítja az ebben a példában használt háttér-API-t, és az Azure-ban a következő oldalon szolgáltatja: `https://conferenceapi.azurewebsites.net?format=json` .

1. A Visual Studio kódban válassza az Azure ikont a tevékenységsávon.
1. Az Explorer panelen bontsa ki a API Management létrehozott új példányt.
1. Kattintson a jobb gombbal az **API-k elemre,** és válassza az **Importálás OpenAPI-hivatkozásból lehetőséget.** 
1. Amikor a rendszer kéri, adja meg a következő értékeket:
    1. JSON formátumú tartalomra mutató **OpenAPI-hivatkozás.** Ebben a példában: *https://conferenceapi.azurewebsites.net?format=json* .
    Ez az URL-cím a példa API-t megvalósító szolgáltatás. API Management a kérelmeket erre a címre továbbítja.
    1. Egy **API-név**, például *demo-conference-api,* amely egyedi a API Management példányban. Ez a név csak betűket, számokat és kötőjeleket tartalmazhat. Az első és az utolsó karakternek alfanumerikusnak kell lennie. Ez a név az API hívásához használt elérési útban használatos.

Az API sikeres importálása után az megjelenik az Explorer panelen, és az elérhető API-műveletek a Műveletek csomópont alatt **jelennek** meg.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Importált API az Explorer panelen":::

## <a name="edit-the-api"></a>Az API szerkesztése

Az API-t a Kód Visual Studio szerkesztheti. Például a szerkesztőablakban szerkessze Resource Manager API JSON-leírását, hogy eltávolítsa az API eléréséhez használt HTTP-protokollt.  Ezután válassza a **Fájl**  >  **mentése lehetőséget.**

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="JSON-leírás szerkesztése":::

Az OpenAPI formátum szerkesztéséhez kattintson a jobb gombbal az API nevére az Explorer panelen, és válassza az **OpenAPI szerkesztése lehetőséget.** Mentse a módosításokat, majd válassza a **Fájl mentése**  >  **lehetőséget.**

## <a name="apply-policies-to-the-api"></a>Szabályzatok alkalmazása az API-ban 

API Management [api-khoz](api-management-policies.md) konfigurálható szabályzatokat biztosít. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A szabályzatok globálisak is, amelyek a API Management-példányban szereplő összes API-ra vonatkoznak, vagy egy adott API- vagy API-művelet hatókörére is vonatkozhatnak.

Ez a szakasz bemutatja, hogyan alkalmazhat néhány gyakori kimenő szabályzatot az API-válasz átalakítására. A példában található szabályzatok módosítják a válaszfejléceket, és elrejtik a válasz törzsében megjelenő eredeti háttér-URL-címeket.

1. Az Explorer panelen az **importált** *demo-conference-api* alatt válassza a Szabályzat lehetőséget. A szabályzatfájl megnyílik a szerkesztőablakban. Ez a fájl az API összes műveletére konfigurál szabályzatokat. 

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

    * Az első `set-header` szabályzat egy egyéni válaszfejlécet ad hozzá bemutatási célokra.
    * A második `set-header` szabályzat törli az **X-Powered-By fejlécet,** ha az létezik. Ez a fejléc felfedheti az API-háttéralkalmazásban használt alkalmazás-keretrendszert, és a közzétevők gyakran eltávolítják.
    * A szabályzat újraírja (maszkok) a hivatkozásokat a válasz törzsében, hogy az átjárón keresztül a megfelelő `redirect-content-urls` hivatkozásokra API Management mutasson.
    
1. Mentse a fájlt. Ha a rendszer kéri, válassza a Feltöltés **lehetőséget** a fájl felhőbe való feltöltéshez.

## <a name="test-the-api"></a>Az API tesztelése

### <a name="get-the-subscription-key"></a>Az előfizetői kulcs lekért

Az importált API és az alkalmazott szabályzatok teszteléséhez szüksége lesz egy előfizetési kulcsra a API Management példányhoz.

1. Az Explorer panelen kattintson a jobb gombbal a API Management nevére.
1. Válassza az **Előfizetési kulcs másolása lehetőséget.**

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Előfizetői kulcs másolása":::

### <a name="test-an-api-operation"></a>API-művelet tesztelése

1. Az Explorer panelen **bontsa** ki a Műveletek csomópontot az importált *demo-conference-api* alatt.
1. Válasszon ki egy műveletet, például a *GetSpeakers* műveletet, majd kattintson a jobb gombbal a műveletre, és válassza a **Művelet tesztelése lehetőséget.**
1. A szerkesztőablakban, az **Ocp-Apim-Subscription-Key** mellett cserélje le a helyére a `{{SubscriptionKey}}` kimásott előfizetői kulcsot.
1. Válassza a **Kérés elküldése** elemet. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="API-kérés küldése a Visual Studio Code-ból":::

Ha a kérés sikeres, a háttér a **200 OK** és néhány adat válaszával válaszol.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API-tesztelési művelet":::

Figyelje meg a válaszban a következő részleteket:
* A **rendszer hozzáadja** az Egyéni fejlécet a válaszhoz.
* Az **X-Powered-By fejléc** nem jelenik meg a válaszban.
* Az API-háttérkiszolgáló URL-címeit a rendszer átirányítja API Management átjáróra, ebben az esetben a következőre: `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>Az API-művelet nyomkövetése

Az API-művelet hibakeresését segítő részletes nyomkövetési információkért válassza az **Ocp-APIM-Trace-Location** melletti hivatkozást. 

Az ezen a helyen található JSON-fájl bejövő, háttér- és kimenő nyomkövetési adatokat tartalmaz, így meghatározhatja, hol fordulnak elő problémák a kérés után.

> [!TIP]
> Az API-műveletek tesztelőjekor a API Management [](api-management-debug-policies.md) bővítmény lehetővé teszi a választható szabályzat-hibakeresést (amely a Fejlesztői szolgáltatási szinten érhető el).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, távolítsa el a  API Management-példányt. Kattintson a jobb gombbal, és válassza a Megnyitás a portálon gombra a API Management [szolgáltatás](get-started-create-service-instance.md#clean-up-resources) és annak erőforráscsoportjának törléséhez.

Másik lehetőségként a Törlés lehetőséget **API Management** hogy csak a API Management-példányt törölje (ez a művelet nem törli annak erőforráscsoportját).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="Új API Management törlése a VS Code-ból":::

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag a API Management Code-hoz Visual Studio funkcióit mutatta be, amelyek API-k importálására és kezelésére használhatók. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * API importálása API Management
> * Az API szerkesztése
> * Alkalmazás API Management szabályzatok
> * Az API tesztelése

A API Management bővítmény további funkciókat biztosít az API-okkal való munkához. Például [hibakeresési (a](api-management-debug-policies.md) Fejlesztői szolgáltatási szinten elérhető) vagy elnevezett értékek létrehozása és [kezelése.](api-management-howto-properties.md)
