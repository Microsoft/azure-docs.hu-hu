---
title: Függvény beállítása az Azure-ban az adatfeldolgozáshoz
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre olyan függvényt az Azure-ban, amely hozzáférhet a digitális ikrekhez, és aktiválhatja azokat.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8ed4e550ea441d5d99a3debb6bf37eb7db2a4a20
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180127"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Function Apps-alkalmazások összekapcsolhatók az Azure-ban az adatfeldolgozáshoz

A digitális ikrek adatok alapján történő frissítése a számítási erőforrásokon keresztül történik az [**esemény-útvonalakon**](concepts-route-events.md) , például a [Azure functions](../azure-functions/functions-overview.md)használatával végzett függvények használatával. A függvények a következőre adott válaszként használhatók a digitális kettős frissítésére:
* az eszköz telemetria érkező adatok IoT Hub
* a tulajdonság változása vagy más, a Twin gráfon belüli digitális Twin-ből származó adatok

Ez a cikk bemutatja, hogyan hozhat létre egy függvényt az Azure-ban az Azure Digital Twins szolgáltatással való használatra. 

Az itt látható lépések áttekintése:

1. Hozzon létre egy Azure Functions-projektet a Visual Studióban
2. Függvény írása [Event Grid](../event-grid/overview.md) triggerrel
3. Hitelesítési kód hozzáadása a függvényhez (az Azure digitális Twins eléréséhez)
4. A függvényalkalmazás közzététele az Azure-ban
5. [Biztonsági](concepts-security.md) hozzáférés beállítása a Function alkalmazáshoz

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Előfeltétel: az Azure Digital Twins-példány beállítása

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Function-alkalmazás létrehozása a Visual Studióban

A Visual Studio 2019-es verziójában válassza a _fájl > új > projekt_ elemet, és keresse meg a _Azure functions_ sablont. Kattintson a _Tovább_ gombra.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Képernyőkép a Visual studióról, amely az új projekt párbeszédpanelt mutatja. A Azure Functions-projekt sablonja ki van emelve.":::

Adja meg a Function alkalmazás nevét, majd válassza a _Létrehozás_ lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="A Visual Studio képernyőképe, amely egy új projekt konfigurálását mutatja be, beleértve a projekt nevét, a hely mentését, az új megoldás létrehozásának lehetőségét, valamint a megoldás nevét.":::

Válassza ki az *Event Grid trigger* függvény alkalmazási típusát, és válassza a _Létrehozás_ lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="A Visual Studio képernyőképe, amely az új Azure Functions alkalmazás létrehozásához szükséges párbeszédpanelt jeleníti meg. A Event Grid trigger beállítás ki van emelve.":::

A Function app létrehozása után a Visual Studio egy **Function1.cs** -fájlban hozza létre a projekt mappájában található kódot. Ez a rövid függvény az események naplózására szolgál.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Képernyőfelvétel a Visual studióról a létrehozott új projekt projekt ablakában. A Function1 nevű minta függvényhez kód található." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Függvény írása Event Grid triggerrel

Az SDK-nak a Function alkalmazásba való felvételével írhat egy függvényt. A Function app a [.net-hez készült Azure Digital Twins SDK (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)használatával kommunikál az Azure Digital Twins szolgáltatással. 

Az SDK használatához a következő csomagokat kell felvennie a projektbe. A csomagokat telepítheti a Visual Studio NuGet csomagkezelő használatával, vagy hozzáadhatja a csomagokat `dotnet` egy parancssori eszköz használatával.

* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System .net. http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. Core](https://www.nuget.org/packages/Azure.Core/)

Ezután a Visual Studio Megoldáskezelőban nyissa meg azt a _Function1.cs_ -fájlt, amelyben a mintakód szerepel, és adja hozzá a következő `using` utasításokat ezekhez a csomagokhoz a függvényhez. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Hitelesítési kód hozzáadása a függvényhez

Ekkor deklarálja az osztály szintjének változóit, és adja hozzá a hitelesítési kódot, amely lehetővé teszi, hogy a függvény hozzáférhessen az Azure digitális Twins szolgáltatáshoz. A következőt fogja hozzáadni a függvényhez a _Function1.cs_ fájlban:.

* Kód az Azure Digital Twins szolgáltatás URL-címének beolvasásához **környezeti változóként**. Érdemes beolvasni a szolgáltatás URL-címét egy környezeti változóból, nem pedig a függvényben rögzített kódolással. A környezeti változó értékét a [cikk későbbi részében](#set-up-security-access-for-the-function-app)állíthatja be. A környezeti változókról további információt a [*Function app kezelése*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)című témakörben talál.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Egy statikus változó, amely egy HttpClient-példányt tart fenn. A HttpClient viszonylag költséges, és azt szeretnénk elkerülni, hogy minden függvény meghívásakor ezt el kellene végezni.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* A felügyelt identitás hitelesítő adatait Azure Functions használhatja.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Vegyen fel egy helyi változót a _DigitalTwinsClient_ belül, hogy az Azure digitális Twins-ügyfél példánya tárolva legyen. Ez a változó *ne* legyen statikus az osztályban.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Vegyen fel egy NULL értékű _adtInstanceUrl_ , és zárja be a függvény logikáját egy try/catch blokkban a kivételek megfogásához.

A módosítások után a függvény kódja a következőhöz hasonló lesz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Most, hogy megtörtént az alkalmazása, közzéteheti az Azure-ban a következő szakaszban ismertetett lépések segítségével.

## <a name="publish-the-function-app-to-azure"></a>A függvényalkalmazás közzététele az Azure-ban

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>Biztonsági hozzáférés beállítása a Function alkalmazáshoz

Az Azure CLI vagy a Azure Portal használatával biztonsági hozzáférést állíthat be a Function alkalmazáshoz. Kövesse az alábbi lépéseket az előnyben részesített lehetőségnél.

# <a name="cli"></a>[Parancssori felület](#tab/cli)

Ezeket a parancsokat [Azure Cloud Shell](https://shell.azure.com) vagy egy [helyi Azure CLI-telepítésben](/cli/azure/install-azure-cli)is futtathatja.

### <a name="assign-access-role"></a>Hozzáférési szerepkör kiosztása

A korábbi példák függvényének csontváza megköveteli, hogy egy tulajdonosi jogkivonatot továbbítson a rendszernek, hogy az Azure Digital Twins szolgáltatással tudjon hitelesíteni. Annak ellenőrzéséhez, hogy a tulajdonosi jogkivonat átadása megtörtént-e, be kell állítania [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) engedélyeket a Function alkalmazás számára az Azure Digital Twins eléréséhez. Ezt csak egyszer kell elvégezni az egyes functions-alkalmazásokhoz.

A Function alkalmazás rendszer által felügyelt identitásával biztosíthatja, hogy az Azure Digital Twins-beli _**adattulajdonosi**_ szerepkört adja meg az Azure digitális Twins-példányához. Ezzel a művelettel a példányban az adatsík-tevékenységek elvégzéséhez a függvény alkalmazás engedélyének kell megadnia. Ezután az Azure Digital Twins-példány URL-címét elérhetővé teheti a függvény számára egy környezeti változó beállításával.

1. A következő parancs használatával megtekintheti a függvény rendszer által felügyelt identitásának részleteit. Jegyezze fel a kimenet _principalId_ mezőjét.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Ha az eredmény üres az identitás részleteinek megjelenítése helyett, hozzon létre egy új, rendszer által felügyelt identitást a függvényhez a következő parancs használatával:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > A kimenet ezután megjeleníti az identitás részleteit, beleértve a következő lépéshez szükséges _principalId_ értéket. 

1. Az alábbi parancsban a _principalId_ érték használatával rendelje hozzá a függvényalkalmazás identitását az Azure Digital Twins-példány _Azure Digital Twins-adattulajdonosi_ szerepköréhez.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Alkalmazásbeállítások konfigurálása

Végül az Azure Digital Twins-példány URL-címét elérhetővé teheti a függvény számára egy **környezeti változó** beállításával. A környezeti változókról további információt a [*Function app kezelése*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)című témakörben talál. 

> [!TIP]
> Az Azure digitális Twins-példány URL-címe az Azure digitális Twins-példány *állomásneve* *https://* hozzáadásával történik. Ha meg szeretné tekinteni az állomásnév nevét, valamint a példány összes tulajdonságát, futtathatja a parancsot `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Hajtsa végre a következő lépéseket a [Azure Portalban](https://portal.azure.com/).

### <a name="assign-access-role"></a>Hozzáférési szerepkör kiosztása

A rendszerhez rendelt felügyelt identitás lehetővé teszi, hogy az Azure-erőforrások hitelesítsék magukat a Cloud Servicesben (például Azure Key Vault) a hitelesítő adatok kódban való tárolása nélkül. Ha engedélyezve van, az összes szükséges engedély az Azure szerepköralapú hozzáférés-vezérlés használatával adható meg. Az ilyen típusú felügyelt identitás életciklusa az erőforrás életciklusához van kötve. Emellett minden erőforráshoz csak egy rendszerhez rendelt felügyelt identitás tartozhat.

1. A [Azure Portal](https://portal.azure.com/)keresse meg a Function alkalmazást úgy, hogy beírja a nevét a keresősávba. Válassza ki az alkalmazást az eredmények közül. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Képernyőkép a Azure Portalról: a függvény alkalmazás neve a portálon, a keresési eredmény pedig ki van jelölve.":::

1. A függvény alkalmazás lapján válassza a bal oldali navigációs sáv _identitás_ elemét, hogy működjön a függvény felügyelt identitásával. A _rendszer által hozzárendelt_ lapon ellenőrizze, hogy az _állapot_ be **van-e kapcsolva (ha** nem, állítsa be most, és *mentse* a változást).

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Képernyőkép a Azure Portalról: a Function alkalmazás Identity (identitás) lapján az állapot beállítás be értékre van állítva." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Válassza ki az Azure szerepkör- _hozzárendelések_ gombot, amely megnyitja az *Azure szerepkör-hozzárendelések* lapot.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Képernyőkép a Azure Portalról: az Azure-függvény identitási lapjának engedélyek területén található, az Azure szerepkör-hozzárendelések gombjának kiemelése." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Válassza a _+ szerepkör-hozzárendelés hozzáadása (előzetes verzió)_ lehetőséget.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Képernyőkép a Azure Portalról: A szerepkör-hozzárendelés (előzetes verzió) és az Azure szerepkör-hozzárendelések oldalának hozzáadása." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. A megnyíló _szerepkör-hozzárendelés hozzáadása (előzetes verzió)_ lapon válassza ki a következő értékeket:

    * **Hatókör**: Erőforráscsoport
    * **Előfizetés**: válassza ki az Azure-előfizetését
    * **Erőforráscsoport**: válassza ki az erőforráscsoportot a legördülő listából
    * **Szerepkör**: válassza ki a legördülő listából az _Azure digitális Twins-adatok tulajdonosát_

    Ezután mentse a részleteket a _Save (Mentés_ ) gomb megnyomásával.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Képernyőkép a Azure Portal: Dialog új szerepkör-hozzárendelés (előzetes verzió) hozzáadásához. A hatókör, az előfizetés, az erőforráscsoport és a szerepkör mezői vannak.":::

### <a name="configure-application-settings"></a>Alkalmazásbeállítások konfigurálása

Ahhoz, hogy a függvény számára elérhetővé váljon az Azure Digital Twins-példány URL-címe, beállíthatja a **környezeti változót** . A környezeti változókról további információt a [*Function app kezelése*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)című témakörben talál. Az Alkalmazásbeállítások környezeti változókként vannak kitéve az Azure Digital Twins-példány eléréséhez. 

Ha környezeti változót szeretne beállítani a példány URL-címével, először töltse le az URL-címet úgy, hogy megkeresi az Azure digitális Twins-példányának állomásnevét. Keresse meg a példányt a [Azure Portal](https://portal.azure.com) keresési sávon. Ezután a bal oldali navigációs sávon az _Áttekintés_ elemre kattintva megtekintheti a _gazdagép nevét_. Másolja ezt az értéket.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Képernyőkép a Azure Portalról: az Azure Digital Twins-példány áttekintés lapján az állomásnév értéke kiemelve jelenik meg.":::

Mostantól a következő lépésekkel hozhat létre egy alkalmazás-beállítást:

1. Keresse meg a Function alkalmazást a portál keresési sávján, és válassza ki az eredmények közül.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Képernyőkép a Azure Portalról: a függvény alkalmazás neve a portálon, a keresési eredmény pedig ki van jelölve.":::

1. A bal oldali navigációs sávon válassza a _konfiguráció_ elemet. Az _Alkalmazásbeállítások_ lapon válassza az _+ új alkalmazás beállítása_ lehetőséget.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Képernyőkép a Azure Portalról: a Function alkalmazás konfiguráció lapján az új Alkalmazásbeállítás létrehozásához szükséges gomb kiemelve jelenik meg.":::

1. A megnyíló ablakban a fent másolt állomásnév érték használatával hozzon létre egy alkalmazás-beállítást.
    * **Név**: ADT_SERVICE_URL
    * **Érték**: https://{saját Azure-digitális-ikrek-Host-Name}
    
    Az alkalmazás beállításainak létrehozásához kattintson _az OK gombra_ .
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Képernyőkép a Azure Portalról: az OK gomb ki van emelve a név és érték mezők kitöltése után az alkalmazás hozzáadása/szerkesztése lapon.":::

1. A beállítás létrehozása után az _alkalmazás beállításai_ lapon megjelenik az üzenet. Ellenőrizze *ADT_SERVICE_URL* megjelenik-e a listában, majd mentse az új alkalmazás-beállítást a _Save (Mentés_ ) gombra kattintva.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Képernyőfelvétel a Azure Portalról: az Alkalmazásbeállítások lapon, az új ADT_SERVICE_URL beállítás kiemelve. A Mentés gomb szintén ki van emelve.":::

1. Az Alkalmazásbeállítások módosításának érvénybe léptetéséhez az alkalmazás újraindítása szükséges, ezért válassza a _Folytatás_ lehetőséget az alkalmazás újraindításához, amikor a rendszer kéri.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Képernyőkép a Azure Portalról: az alkalmazás újraindítását és az alkalmazás újraindítását érintő módosítások megfigyelhető. A Folytatás gomb ki van emelve.":::

---

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben követte, hogyan állíthat be egy Function alkalmazást az Azure-ban az Azure digitális Twins szolgáltatással való használatra.

Következő lépésként tekintse meg az alapszintű függvények felépítését IoT Hub adatok Azure digitális Ikrekbe való betöltéséhez:
* [*Útmutató: telemetria beolvasása IoT Hubból*](how-to-ingest-iot-hub-data.md)
