---
title: Függvény beállítása az Azure-ban az adatfeldolgozáshoz
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre olyan függvényt az Azure-ban, amely hozzáférhet a digitális ikrekhez, és aktiválhatja azokat.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: ecbf07c1a85c708d4117c83bca546cd8bd6ccd2b
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546074"
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

Az SDK használatához a következő csomagokat kell felvennie a projektbe. A csomagokat telepítheti a Visual Studio NuGet csomagkezelő használatával, vagy hozzáadhatja a csomagokat `dotnet` egy parancssori eszköz használatával. Kövesse az alábbi lépéseket az előnyben részesített módszerhez.

**1. lehetőség. Csomagok hozzáadása a Visual Studio Package Managerrel:**
    
Kattintson a jobb gombbal a projektre, és válassza a _NuGet-csomagok kezelése_ elemet a listából. Ezután a megnyíló ablakban válassza a _Tallózás_ lapot, és keresse meg a következő csomagokat. Válassza a _telepítés_ lehetőséget, és _fogadja el_ a licencszerződést a csomagok telepítéséhez.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core`

**2. lehetőség. Csomagok hozzáadása a `dotnet` parancssori eszközzel:**

Azt is megteheti, hogy a következő `dotnet add` parancsokat használja egy parancssori eszközben:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core
```

Ezután a Visual Studio Megoldáskezelő nyissa meg azt a _Function1.cs_ -fájlt, amelyben a mintakód szerepel, és adja hozzá a következő `using` utasításokat a függvényhez. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Hitelesítési kód hozzáadása a függvényhez

Ekkor deklarálja az osztály szintjének változóit, és adja hozzá a hitelesítési kódot, amely lehetővé teszi, hogy a függvény hozzáférhessen az Azure digitális Twins szolgáltatáshoz. A következőt fogja hozzáadni a függvényhez a _Function1.cs_ fájlban:.

* Kód az Azure Digital Twins szolgáltatás URL-címének beolvasásához környezeti változóként. Érdemes beolvasni a szolgáltatás URL-címét egy környezeti változóból, nem pedig a függvényben rögzített kódolással.

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

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>1. lehetőség: a Function alkalmazás biztonsági hozzáférésének beállítása a parancssori felület használatával

A korábbi példák függvényének csontváza megköveteli, hogy egy tulajdonosi jogkivonatot továbbítson a rendszernek, hogy az Azure Digital Twins szolgáltatással tudjon hitelesíteni. A tulajdonosi jogkivonat átadásának biztosítása érdekében be kell állítania [Managed Service Identity (MSI)](../active-directory/managed-identities-azure-resources/overview.md) funkciót a Function alkalmazáshoz. Ezt csak egyszer kell elvégezni az egyes functions-alkalmazásokhoz.

Létrehozhatja a rendszer által felügyelt identitást, és hozzárendelheti a Function alkalmazás identitását az Azure Digital _**Twins-adattulajdonosi**_ szerepkörhöz az Azure Digital Twins-példányhoz. Ezzel a művelettel a példányban az adatsík-tevékenységek elvégzéséhez a függvény alkalmazás engedélyének kell megadnia. Ezután az Azure Digital Twins-példány URL-címét elérhetővé teheti a függvény számára egy környezeti változó beállításával.

A parancsok futtatásához használja a [Azure Cloud Shell](https://shell.azure.com) .

A rendszerfelügyelt identitás létrehozásához használja a következő parancsot. Jegyezze fel a kimenet _principalId_ mezőjét.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Az alábbi parancsban a _principalId_ érték használatával rendelje hozzá a függvényalkalmazás identitását az Azure Digital Twins-példány _Azure Digital Twins-adattulajdonosi_ szerepköréhez.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Végül elérhetővé teheti az Azure Digital Twins-példány URL-címét a függvény számára egy környezeti változó beállításával. A környezeti változók beállításával kapcsolatos további információkért lásd: [*környezeti változók*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> Az Azure digitális Twins-példány URL-címe az Azure digitális Twins-példány *állomásneve* *https://* hozzáadásával történik. Az állomásnév, valamint a példány összes tulajdonságának megtekintéséhez futtathatja a parancsot `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>2. lehetőség: biztonsági hozzáférés beállítása a Function alkalmazáshoz Azure Portal használatával

A rendszerhez rendelt felügyelt identitás lehetővé teszi, hogy az Azure-erőforrások hitelesítsék magukat a Cloud Servicesben (például Azure Key Vault) a hitelesítő adatok kódban való tárolása nélkül. Ha engedélyezve van, az összes szükséges engedély az Azure szerepköralapú hozzáférés-vezérlés használatával adható meg. Az ilyen típusú felügyelt identitás életciklusa az erőforrás életciklusához van kötve. Emellett minden erőforrás (például a virtuális gép) csak egy rendszerhez rendelt felügyelt identitással rendelkezhet.

A [Azure Portal](https://portal.azure.com/)keressen rá a _Function app_ kifejezésre a keresési sávban a korábban létrehozott Function alkalmazás nevével. Válassza ki a *függvényalkalmazás* a listából. 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Képernyőkép a Azure Portalról: a függvény alkalmazás neve a portálon, a keresési eredmény pedig ki van jelölve.":::

A felügyelt identitás engedélyezéséhez kattintson a bal oldali navigációs sávban az _Identity (identitás_ ) elemre a függvény alkalmazás ablakban.
A _rendszer-hozzárendelés_ lapon állítsa be az _állapotot_ a be értékre, és _mentse_ azt. Egy előugró ablak jelenik meg, amely _lehetővé teszi a rendszerhez rendelt felügyelt identitás engedélyezését_.
Válassza az _Igen_ gomb lehetőséget. 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Képernyőkép a Azure Portalról: a Function alkalmazás Identity (identitás) lapján a rendszerhez rendelt felügyelt identitás engedélyezése beállítás Igen értékre van állítva. Az állapot beállítás be értékre van állítva.":::

Az értesítésekben ellenőrizheti, hogy a függvény sikeresen regisztrálva van-e Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Képernyőkép a Azure Portalról: az értesítések listája a portál felső sávján található harang alakú ikon kiválasztásával. Értesítést kap arról, hogy a felhasználó engedélyezte a rendszerhez rendelt felügyelt identitást.":::

Jegyezze fel az _Identity (identitás_ ) lapon megjelenő **objektumazonosítót** is, ahogy azt a következő szakaszban fogja használni.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Képernyőkép a Azure Portalről: az Azure Function identitás oldalának objektum-azonosító mezője körüli kiemelés.":::

### <a name="assign-access-roles-using-azure-portal"></a>Hozzáférési szerepkörök kiosztása Azure Portal használatával

Válassza ki az Azure szerepkör- _hozzárendelések_ gombot, amely megnyitja az *Azure szerepkör-hozzárendelések* lapot. Ezután válassza a _+ szerepkör-hozzárendelés hozzáadása (előzetes verzió)_ lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Képernyőkép a Azure Portalról: az Azure-függvény identitási lapjának engedélyek területén található, az Azure szerepkör-hozzárendelések gombjának kiemelése.":::

A megnyíló _szerepkör-hozzárendelés hozzáadása (előzetes verzió)_ lapon válassza a következőket:

* _Hatókör_: Erőforráscsoport
* _Előfizetés_: válassza ki az Azure-előfizetését
* _Erőforráscsoport_: válassza ki az erőforráscsoportot a legördülő listából
* _Szerepkör_: válassza ki a legördülő listából az _Azure digitális Twins-adatok tulajdonosát_

Ezután mentse a részleteket a _Save (Mentés_ ) gomb megnyomásával.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Képernyőkép a Azure Portal: Dialog új szerepkör-hozzárendelés (előzetes verzió) hozzáadásához. A hatókör, az előfizetés, az erőforráscsoport és a szerepkör mezői vannak.":::

### <a name="configure-application-settings-using-azure-portal"></a>Alkalmazásbeállítások konfigurálása Azure Portal használatával

Az Azure Digital Twins-példány URL-címét elérhetővé teheti a függvény számára egy környezeti változó beállításával. Erről további információt a [*környezeti változók*](/sandbox/functions-recipes/environment-variables)című témakörben talál. Az Alkalmazásbeállítások környezeti változókként vannak kitéve a digitális Twins-példány eléréséhez. 

Ha környezeti változót szeretne beállítani a példány URL-címével, először töltse le az URL-címet úgy, hogy megkeresi az Azure digitális Twins-példányának állomásnevét. Keresse meg a példányt a [Azure Portal](https://portal.azure.com) keresési sávon. Ezután a bal oldali navigációs sávon az _Áttekintés_ elemre kattintva megtekintheti a _gazdagép nevét_. Másolja ezt az értéket.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Képernyőkép a Azure Portalról: az Azure Digital Twins-példány áttekintés lapján az állomásnév értéke kiemelve jelenik meg.":::

Mostantól az alábbi lépéseket követve létrehozhat egy alkalmazás-beállítást:

1. Keresse meg a Function alkalmazást a portál keresési sávján, és válassza ki az eredmények közül.
1. Válassza ki a bal oldali navigációs sávon a _konfiguráció_ elemet egy új Alkalmazásbeállítás létrehozásához
1. Az _Alkalmazásbeállítások_ lapon válassza az _+ új alkalmazás beállítása_ lehetőséget.

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Képernyőkép a Azure Portalról: a függvény alkalmazás neve a portálon, a keresési eredmény pedig ki van jelölve.":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Képernyőkép a Azure Portalról: a Function alkalmazás konfiguráció lapján az új Alkalmazásbeállítás létrehozásához szükséges gomb kiemelve jelenik meg.":::

A megnyíló ablakban a fent másolt állomásnév érték használatával hozzon létre egy alkalmazás-beállítást.
* **Név**: ADT_SERVICE_URL
* **Érték**: https://{saját Azure-digitális-ikrek-Host-Name}

Az alkalmazás beállításainak létrehozásához kattintson _az OK gombra_ .

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Képernyőkép a Azure Portalról: az OK gomb ki van emelve a név és érték mezők kitöltése után az alkalmazás hozzáadása/szerkesztése lapon.":::

Az Alkalmazásbeállítások a _Name (név_ ) mezőben tekinthetők meg az alkalmazás nevével. Ezután mentse az alkalmazás beállításait a _Save (Mentés_ ) gombra kattintva.

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Képernyőfelvétel a Azure Portalról: az Alkalmazásbeállítások lapon, az új ADT_SERVICE_URL beállítás kiemelve. A Mentés gomb szintén ki van emelve.":::

Az Alkalmazásbeállítások változásainak érvénybe léptetéséhez az alkalmazás újraindítása szükséges. Válassza a _Folytatás_ lehetőséget az alkalmazás újraindításához.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Képernyőkép a Azure Portalról: az alkalmazás újraindítását és az alkalmazás újraindítását érintő módosítások megfigyelhető. A Folytatás gomb ki van emelve.":::

Az Alkalmazásbeállítások az _értesítések_ ikon kiválasztásával tekinthetők meg. Ha az alkalmazás beállítása nincs létrehozva, akkor a fenti folyamat követésével újra megpróbálkozhat az alkalmazás hozzáadásával.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Képernyőkép a Azure Portalról: az értesítések listája a portál felső sávján található harang alakú ikon kiválasztásával. Megjelenik egy értesítés arról, hogy a webalkalmazás beállításai sikeresen frissítve lettek.":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben követte, hogyan állíthat be egy Function alkalmazást az Azure-ban az Azure digitális Twins szolgáltatással való használatra.

Következő lépésként tekintse meg az alapszintű függvények felépítését IoT Hub adatok Azure digitális Ikrekbe való betöltéséhez:
* [*Útmutató: telemetria beolvasása IoT Hubból*](how-to-ingest-iot-hub-data.md)
