---
title: Függvény beállítása az Azure-ban az adatok feldolgozásához
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan hozhat létre olyan függvényt az Azure-ban, amely digitális ikereszközekkel is elérhető és aktiválható.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480775"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Függvényalkalmazások csatlakoztatása az Azure-ban az adatok feldolgozásához

A digitális ikereket adatok alapján lehet frissíteni a számítási erőforrásokon keresztüli [eseményútvonalak](concepts-route-events.md) használatával. Például egy függvény, amely a függvény használatával készült, [Azure Functions](../azure-functions/functions-overview.md) a következő válaszként frissítheti a digitális ikereket:
* Eszköz telemetriai adatai a Azure IoT Hub.
* Egy tulajdonság módosítása vagy egyéb adatok egy másik digitális ikereszközről az ikergráfon belül.

Ez a cikk bemutatja, hogyan hozhat létre függvényt az Azure-ban a Azure Digital Twins. Függvény létrehozásához az alábbi alapvető lépéseket kell követnie:

1. Hozzon létre egy Azure Functions projektet a Visual Studio.
2. Írjon egy függvényt, amely rendelkezik [Azure Event Grid](../event-grid/overview.md) eseményindítóval.
3. Adja hozzá a hitelesítési kódot a függvényhez, hogy hozzáfér Azure Digital Twins.
4. A függvényalkalmazás közzététele az Azure-ban.
5. Állítsa be [a függvényalkalmazás](concepts-security.md) biztonságát.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Előfeltétel: A Azure Digital Twins

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Függvényalkalmazás létrehozása a Visual Studio

A Visual Studio 2019-ben válassza a File New Project **(Fájl** új  >    >  **projekt) lehetőséget.** Keresse meg a **Azure Functions** sablont. Kattintson a **Tovább** gombra.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Az új Visual Studio párbeszédpanel képernyőképe. A Azure Functions projektsablon ki van emelve.":::

Adja meg a függvényalkalmazás nevét, majd válassza a __Létrehozás lehetőséget.__

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Képernyőkép az Visual Studio új projekt konfigurálni szolgáló párbeszédpanelről. A beállítások közé tartozik a projekt neve, a mentés helye, az új megoldás létrehozása és a megoldás neve.":::

Válassza ki az eseményindítóhoz **Event Grid a függvényalkalmazás típusát,** majd válassza a __Létrehozás lehetőséget.__

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Képernyőkép Visual Studio új alkalmazás létrehozásához szükséges párbeszédpanel Azure Functions megjelenítéséről. A Event Grid eseményindító lehetőség ki van emelve.":::

A függvényalkalmazás létrehozása után a Visual Studio létrehoz egy kódmintát egy *Function1.cs* fájlban a projektmappában. Ez a rövid függvény az események naplózását használja.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Képernyőkép az Visual Studio. Megjelenik az új projekt projektablaka. A minta függvény kódja egy Function1 nevű fájlban jelenik meg." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Függvény írása, amely rendelkezik Event Grid eseményindítóval

Függvényt úgy írhat, hogy hozzáad egy SDK-t a függvényalkalmazáshoz. A függvényalkalmazás az Azure Digital Twins SDK for [.NET (C#)](/dotnet/api/overview/azure/digitaltwins/client)Azure Digital Twins használatával lép interakcióba. 

Az SDK csak akkor használható, ha az alábbi csomagokat foglalja bele a projektbe. Telepítse a csomagokat a Visual Studio NuGet-csomagkezelővel. Vagy adja hozzá a csomagokat az paranccsal `dotnet` egy parancssori eszközben.

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

Ezután a Visual Studio Megoldáskezelő nyissa meg a mintakódot tartalmazó _Function1.cs_ fájlt. Adja hozzá a `using` következő utasításokat a csomagokhoz.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Hitelesítési kód hozzáadása a függvényhez

Most deklaráljuk az osztályszintű változókat, és hozzáadunk egy hitelesítési kódot, amely lehetővé teszi, hogy a függvény hozzáférjen Azure Digital Twins. Adja hozzá a változókat és a kódot a függvényhez a _Function1.cs fájlban._

* **Kód a Azure Digital Twins szolgáltatás URL-címének környezeti változóként való beolvassa.** A szolgáltatás URL-címét egy környezeti változóból lehet beolvasni ahelyett, hogy a függvényben nem változtatható meg. Ennek a környezeti változónak az értékét a cikk későbbi része [fogja beállítani.](#set-up-security-access-for-the-function-app) További információ a környezeti változókról: [A függvényalkalmazás kezelése.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Egy statikus változó egy HttpClient-példányhoz.** A HttpClient létrehozása viszonylag költséges, ezért nem szeretnénk minden függvényhíváshoz létrehozni.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Felügyelt identitás hitelesítő adatai.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Egy helyi _DigitalTwinsClient változó._** Adja hozzá a függvényen belüli változót, hogy az Azure Digital Twins ügyfélpéldányt. Ezt *a* változót ne tegye statikusra az osztályon belül.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Az _adtInstanceUrl null értékű ellenőrzése._** Adja hozzá a null ellenőrzést, majd burkolja a függvény logikáját egy try/catch blokkba a kivételek befogása érdekében.

A módosítások után a függvénykód az alábbi példához hasonló lesz.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Most, hogy megírta az alkalmazást, közzéteheti az Azure-ban.

## <a name="publish-the-function-app-to-azure"></a>A függvényalkalmazás közzététele az Azure-ban

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>A függvény közzétételének ellenőrzése

1. Jelentkezzen be az [Azure Portal.](https://portal.azure.com/)
2. Az ablak tetején található keresőmezőben keresse meg a függvényalkalmazás nevét, majd válassza ki.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Képernyőkép a Azure Portal. A keresőmezőbe írja be a függvényalkalmazás nevét." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. A **megnyíló Függvényalkalmazás** oldalon a bal oldali menüben válassza a Függvények **lehetőséget.** Ha a függvényt sikeresen közzétette, a neve megjelenik a listában.

    > [!Note] 
    > Előfordulhat, hogy néhány percet várnia kell, vagy néhányszor frissítenie kell az oldalt, mielőtt a függvény megjelenik a közzétett függvények listájában.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="A közzétett függvények megtekintése a Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

A Azure Digital Twins eléréséhez a függvényalkalmazásnak egy rendszer által felügyelt identitásra van szüksége, amely engedéllyel rendelkezik a Azure Digital Twins eléréséhez. Ezt a következő lépésként fogja beállítani.

## <a name="set-up-security-access-for-the-function-app"></a>Biztonsági hozzáférés beállítása a függvényalkalmazáshoz

A függvényalkalmazás biztonsági hozzáférését az Azure CLI vagy a Azure Portal. Kövesse az előnyben részesített beállításhoz szükséges lépéseket.

# <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa ezeket a parancsokat [a Azure Cloud Shell](https://shell.azure.com) vagy egy helyi [Azure CLI-telepítésben.](/cli/azure/install-azure-cli)
A függvényalkalmazás rendszer által felügyelt identitásával adhat  neki Azure Digital Twins-adattulajdonos szerepkört a Azure Digital Twins számára. A szerepkör engedélyt ad a függvényalkalmazásnak a példányban az adatsík tevékenységeinek elvégzésére. Ezután tegye elérhetővé a példány URL-címét a függvény számára egy környezeti változó beállításával.

### <a name="assign-an-access-role"></a>Hozzáférési szerepkör hozzárendelése

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

A korábbi példákban a függvény vázához egy jogkivonatot kell átadni. Ha a bearer token nem lesz átvéve, a függvényalkalmazás nem tud hitelesítést végezni a Azure Digital Twins. 

Annak érdekében, hogy a rendszer át [](../active-directory/managed-identities-azure-resources/overview.md) tudja-e a jogkivonatot, állítsa be a felügyelt identitások engedélyeinek beállítását, hogy a függvényalkalmazás hozzáfér Azure Digital Twins. Ezeket az engedélyeket minden függvényalkalmazáshoz csak egyszer kell beállítania.


1. A következő paranccsal láthatja a függvény rendszer által felügyelt identitásának részleteit. Jegyezze fel `principalId` a kimenetben a mezőt.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Ha az eredmény üres az identitásadatok megjelenítése helyett, hozzon létre egy új, rendszer által felügyelt identitást a függvényhez a következő paranccsal:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > A kimenet megjeleníti az identitás részleteit, beleértve a következő `principalId` lépéshez szükséges értéket. 

1. A következő parancsban az értékkel rendelje hozzá a függvényalkalmazás identitását a `principalId` _Azure Digital Twins-adattulajdonos_ szerepkörhöz a Azure Digital Twins példányhoz.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Alkalmazásbeállítások konfigurálása

Tegye elérhetővé a példány URL-címét a függvény számára egy környezeti változó beállításával. További információ a környezeti változókról: [A függvényalkalmazás kezelése.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

> [!TIP]
> A Azure Digital Twins-példány URL-címe úgy készül, hogy hozzáad https:// a példány gazdanevének elejéhez.  A gazdagép nevének és a példány összes tulajdonságának a nevére a parancs futtatásával láthatja `az dt show --dt-name <your-Azure-Digital-Twins-instance>` a parancsot.

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Kövesse az alábbi lépéseket a [Azure Portal.](https://portal.azure.com/)

### <a name="assign-an-access-role"></a>Hozzáférési szerepkör hozzárendelése

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

A rendszer által hozzárendelt felügyelt identitásokkal az Azure-erőforrások anélkül hitelesíthetők a felhőszolgáltatásokban (Azure Key Vault), hogy a hitelesítő adatokat kódban tárolják. A rendszer által hozzárendelt felügyelt identitás engedélyezése után az összes szükséges engedély az Azure szerepköralapú hozzáférés-vezérlésével adható meg. 

Az ilyen típusú felügyelt identitások életciklusa az erőforrás életciklusához kötődik. Emellett minden erőforrásnak csak egy, a rendszer által hozzárendelt felügyelt identitása lehet.

1. A [Azure Portal](https://portal.azure.com/)a keresőmezőbe begépelt névvel keresse meg a függvényalkalmazást. Válassza ki az alkalmazást az eredmények közül. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Képernyőkép a Azure Portal. A függvényalkalmazás neve a portál keresősávjában található, és a keresési eredmény ki van emelve.":::

1. A függvényalkalmazás oldalának bal oldali menüjében válassza az __Identitás__ lehetőséget a függvény felügyelt identitásának munkához. A Rendszer __által hozzárendelt lapon__ ellenőrizze, hogy az __Állapot__ be van-e **állítva.** Ha nem, állítsa be most, majd mentse **a** változtatást.

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Képernyőkép a Azure Portal. A függvényalkalmazás Identitás lapján az Állapot beállítás Be van állítva." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Válassza __az Azure-beli szerepkör-hozzárendelések lehetőséget.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Képernyőkép a Azure Portal. Az Azure-függvény Identitás lapján, az Engedélyek alatt ki van emelve az Azure-beli szerepkör-hozzárendelések gomb." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Válassza __a + Szerepkör-hozzárendelés hozzáadása (előzetes verzió) lehetőséget.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Képernyőkép a Azure Portal. Az Azure-beli szerepkör-hozzárendelések lapon ki van emelve a Szerepkör-hozzárendelés hozzáadása (előzetes verzió) gomb." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. A __Szerepkör-hozzárendelés hozzáadása (előzetes verzió)__ lapon válassza ki a következő értékeket:

    * **Hatókör:** _Erőforráscsoport_
    * **Előfizetés**: Válassza ki az Azure-előfizetést.
    * **Erőforráscsoport:** Válassza ki az erőforráscsoportot.
    * **Szerepkör:** _Azure Digital Twins adattulajdonos_

    Mentse a részleteket a Mentés __lehetőség kiválasztásával.__

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Képernyőkép a Azure Portal új szerepkör-hozzárendelés hozzáadásáról. A párbeszédpanelen a Hatókör, Előfizetés, Erőforráscsoport és Szerepkör mezők jelennek meg.":::

### <a name="configure-application-settings"></a>Alkalmazásbeállítások konfigurálása

Ahhoz, hogy a Azure Digital Twins URL-címe elérhető legyen a függvény számára, beállíthat egy környezeti változót. Az alkalmazásbeállítások környezeti változókként vannak elérhetővé téve a Azure Digital Twins számára. További információ a környezeti változókról: [A függvényalkalmazás kezelése.](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal) 

Ha egy környezeti változót a példány URL-címével kell beállítania, először keresse meg a példány gazdagépnevét: 

1. Keresse meg a példányt a [Azure Portal.](https://portal.azure.com) 
1. A bal oldali menüben válassza az Áttekintés __lehetőséget.__ 
1. Másolja ki __az Állomásnév__ értéket.

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Képernyőkép a Azure Portal. A példány Áttekintés lapján a gazdagépnév értéke ki van emelve.":::

Most már létrehozhat egy alkalmazásbeállítást:

1. A portál keresősávjában keresse meg a függvényalkalmazást, majd válassza ki az eredmények közül.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Képernyőkép a Azure Portal. A függvényalkalmazás nevére a portál keresősávjában van rákeresve. A keresési eredmény ki van emelve.":::

1. A bal oldalon válassza a Konfiguráció __lehetőséget.__ Ezután az __Alkalmazásbeállítások lapon__ válassza __az + Új alkalmazásbeállítás lehetőséget.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Képernyőkép a Azure Portal. A függvényalkalmazás Konfiguráció lapján ki van emelve az Új alkalmazás beállítás létrehozására használt gomb.":::

1. A megnyíló ablakban használja a kimáselt állomásnév értéket egy alkalmazásbeállítás létrehozásához.
    * **Név:** ADT_SERVICE_URL
    * **Érték:** https://{azure-digital-twins-host-name}
    
    Kattintson __az OK__ gombra egy alkalmazásbeállítás létrehozásához.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Képernyőkép a Azure Portal. Az Alkalmazásbeállítás hozzáadása/szerkesztése oldalon a Név és az Érték mezők ki vannak töltve. Az O K gomb ki van emelve.":::

1. A beállítás létrehozása után meg kell jelenni az Alkalmazásbeállítások __lapon.__ Ellenőrizze, **ADT_SERVICE_URL** megjelenik-e a listában. Ezután mentse az új alkalmazásbeállítást a Mentés __lehetőség kiválasztásával.__

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Képernyőkép a Azure Portal. Az alkalmazásbeállítások lapon ki van emelve az új A D T SERVICE U R L beállítás. A Mentés gomb is ki van emelve.":::

1. Az alkalmazásbeállítások módosításához újra kell indítani  az alkalmazást, ezért ha a rendszer kéri, válassza a Folytatás lehetőséget az alkalmazás újraindításához.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Képernyőkép a Azure Portal. Megjegyzés: az alkalmazásbeállítások bármely módosítása újraindítja az alkalmazást. A Folytatás gomb ki van emelve.":::

---

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy függvényalkalmazást fog beállítani az Azure-ban a Azure Digital Twins. Ezután tekintse meg, hogyan építhet fel egy alapszintű függvényt az adatok IoT Hub [a](how-to-ingest-iot-hub-data.md)Azure Digital Twins.
