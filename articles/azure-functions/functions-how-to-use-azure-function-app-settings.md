---
title: Függvényalkalmazás beállításainak konfigurálása a Azure Functions
description: Megtudhatja, hogyan konfigurálhatja a függvényalkalmazás beállításait a Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 6775fdf8d5174600344f3c7177a3130ef63e8f76
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832679"
---
# <a name="manage-your-function-app"></a>A függvényalkalmazás kezelése 

Ebben Azure Functions függvényalkalmazás biztosítja az egyes függvények végrehajtási környezetét. A függvényalkalmazások viselkedése az adott függvényalkalmazás által üzemeltetett összes függvényre vonatkozik. A függvényalkalmazás összes függvényének azonos nyelvűnek kell [lennie.](supported-languages.md) 

A függvényalkalmazás egyes függvényeket együtt helyez üzembe, és együtt méretez. Ugyanabban a függvényalkalmazásban minden függvény osztozik az erőforrásokon példányonként, ahogy a függvényalkalmazás skáláz. 

A kapcsolati sztringeket, a környezeti változókat és az egyéb alkalmazásbeállításokat minden függvényalkalmazáshoz külön kell meghatározni. A függvényalkalmazások között megosztani szükséges adatokat külsőleg, megőrzött tárolóban kell tárolni.

## <a name="get-started-in-the-azure-portal"></a>Első lépések az Azure Portalon

1. Először is a portálra [Azure Portal,] és jelentkezzen be Azure-fiókjába. A portál tetején található keresősávba írja be a függvényalkalmazás nevét, és válassza ki a listából. 

2. A **bal oldali** panel Beállítások panelen válassza a Konfiguráció **lehetőséget.**

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Függvényalkalmazás áttekintése a Azure Portal":::

Az áttekintő oldalon mindent áttekinthet, amire szüksége van a **[](#settings)** függvényalkalmazás kezeléséhez, különös tekintettel az alkalmazásbeállításokra és a **[platformfunkciókra.](#platform-features)**

## <a name="work-with-application-settings"></a><a name="settings"></a>Alkalmazásbeállítások használata

Az alkalmazásbeállításokat a [](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) virtuális Azure Portal az [Azure CLI](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) és [a](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings)Azure PowerShell. Az alkalmazásbeállításokat az Visual Studio [Code-ból](functions-develop-vs-code.md#application-settings-in-azure) és a [Visual Studio.](functions-develop-vs.md#function-app-settings) 

Ezek a beállítások titkosítva vannak tárolva. További információ: Alkalmazásbeállítások [biztonsága.](security-concepts.md#application-settings)

# <a name="portal"></a>[Portál](#tab/portal)

Az alkalmazásbeállításokat az [Első](#get-started-in-the-azure-portal)lépések a Azure Portal. 

Az **Alkalmazásbeállítások** lap fenntartja a függvényalkalmazás által használt beállításokat. Az értékeknek **a portálon való** csak az Értékek megjelenítése lehetőséget kell választania. Beállítás hozzáadásához a portálon válassza az **Új** alkalmazásbeállítás lehetőséget, és adja hozzá az új kulcs-érték párt.

![Függvényalkalmazás beállításai a Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

A [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_list) parancs visszaadja a meglévő alkalmazásbeállításokat, ahogyan az alábbi példában is:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

A [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) parancs hozzáad vagy frissíti az alkalmazásbeállítást. Az alábbi példa létrehoz egy beállítást egy nevű kulccsal `CUSTOM_FUNCTION_APP_SETTING` és `12345` értékkel:


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

A [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) parancsmag visszaadja a meglévő alkalmazásbeállításokat, ahogyan az alábbi példában is: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

A [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) parancs hozzáad vagy frissíti az alkalmazásbeállítást. Az alábbi példa létrehoz egy beállítást egy nevű kulccsal `CUSTOM_FUNCTION_APP_SETTING` és `12345` értékkel:

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Alkalmazásbeállítások használata

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Amikor helyileg fejleszt függvényalkalmazást, ezeknek az értékeknek helyi másolatokat kell fenntartania a projektfájlban local.settings.jsfájlban. További tudnivalókért lásd: [Helyi beállítások fájlja.](functions-run-local.md#local-settings-file)

## <a name="hosting-plan-type"></a>Üzemeltetési csomag típusa

Amikor létrehoz egy függvényalkalmazást, egy üzemeltetési tervet is létrehoz, amelyben az alkalmazás fut. Egy csomag egy vagy több függvényalkalmazással is rendelkezik. A függvények funkciói, skálázása és díjszabása a csomag típusától függ. További tudnivalókért lásd a Azure Functions [lehetőségeket.](functions-scale.md)

A függvényalkalmazás által használt csomag típusát a Azure Portal, az Azure CLI vagy a Azure PowerShell API-k használatával állapíthatja meg. 

A terv típusát az alábbi értékek jelzik:

| Csomag típusa | Portál | Azure CLI/PowerShell |
| --- | --- | --- |
| [Használat](consumption-plan.md) | **Használat** | `Dynamic` |
| [Prémium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedikált (App Service)](dedicated-plan.md) | Különböző | Különböző |

# <a name="portal"></a>[Portál](#tab/portal)

A függvényalkalmazás által használt terv típusának meghatározásához tekintse  meg a függvényalkalmazás Áttekintés **App Service** tervét a [Azure Portal.](https://portal.azure.com) A tarifacsomagot úgy láthatja, hogy kiválasztja App Service  csomag nevét, majd a bal oldali panelen válassza a Tulajdonságok lehetőséget. 

![Méretezési terv megtekintése a portálon](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Futtassa a következő Azure CLI-parancsot az üzemeltetési csomag típusának lekért futtatásához:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

Az előző példában cserélje le a és a helyére `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` az erőforráscsoportot és a függvényalkalmazás nevét. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa a következő Azure PowerShell parancs futtatásával a futtatott csomag típusának lekért adatokat:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
Az előző példában cserélje le a és a helyére `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` az erőforráscsoportot és a függvényalkalmazás nevét. 

---

## <a name="plan-migration"></a>A migrálás megterve

Az Azure CLI-parancsokkal egy függvényalkalmazást minkálhat egy használat alapú csomag és egy Prémium szintű csomag között Windows rendszeren. Az egyes parancsok a migrálás irányától függnek. A dedikált (App Service) csomagra történő közvetlen migrálás jelenleg nem támogatott.

Ez a migrálás Linux rendszeren nem támogatott.

### <a name="consumption-to-premium"></a>Használat prémium szintűre

A használat alapján csomagról Prémium szintű csomagra való áttelepítéshez használja a következő eljárást Windows rendszeren:

1. Az alábbi parancs futtatásával hozzon létre egy új App Service (Elastic Premium) ugyanabban a régióban és erőforráscsoportban, mint a meglévő függvényalkalmazás.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Futtassa a következő parancsot a meglévő függvényalkalmazás új prémium szintű csomagba való áttelepítéséhez

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Ha már nincs szüksége a korábbi használatra szánt függvényalkalmazás-csomagra, törölje az eredeti függvényalkalmazás-csomagját, miután meggyőződott arról, hogy sikeresen migrált az újba. Futtassa a következő parancsot az erőforráscsoportban található összes használatra szánt csomag listájának lekért listájához.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    Biztonságosan törölheti a tervet nulla helyen, amelyről áttelepített.

1. Futtassa a következő parancsot a migrált használatú csomag törléséhez.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Prémiumról használatra

Az alábbi eljárást használhatja prémium szintű csomagról használat alapú csomagra Windows rendszeren:

1. Az alábbi parancs futtatásával hozzon létre egy új függvényalkalmazást (használatban) ugyanabban a régióban és erőforráscsoportban, mint a meglévő függvényalkalmazás. Ez a parancs egy új használatra szánt tervet is létrehoz, amelyben a függvényalkalmazás fut.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Futtassa a következő parancsot a meglévő függvényalkalmazás az új használatban lévő csomagba való áttelepítéséhez.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Törölje az 1. lépésben létrehozott függvényalkalmazást, mivel csak a meglévő függvényalkalmazás futtatásához létrehozott csomagra van szükség.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Ha már nincs szüksége az előző Premium-függvényalkalmazás-csomagra, törölje az eredeti függvényalkalmazás-csomagját, miután meggyőződott arról, hogy sikeresen migrált az újba. Vegye figyelembe, hogy ha a csomag nem törlődik, a prémium szintű csomagért továbbra is díjat számítunk fel. Futtassa a következő parancsot az erőforráscsoportban található összes Prémium csomag listájának lekért listájához.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Futtassa a következő parancsot a migrált Prémium csomag törléséhez.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Platform funkciói

A függvényalkalmazások a következő platformon futnak és Azure App Service karbantartva. Így a függvényalkalmazások hozzáférhetnek az Azure alapvető webes üzemeltetési platform legtöbb funkciójéhez. A bal oldali panelen használhatja a függvényalkalmazásokban App Service platform számos funkcióját. 

> [!NOTE]
> Nem minden App Service funkció érhető el, ha egy függvényalkalmazás a használat alapján futtatott üzemeltetési csomagon fut.

A cikk további részében az alábbi App Service funkciókkal Azure Portal, amelyek hasznosak a Functions számára:

+ [App Service szerkesztő](#editor)
+ [Console](#console)
+ [Speciális eszközök (Kudu)](#kudu)
+ [Üzembe helyezési lehetőségek](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)

További információ a beállításokkal való App Service: [Configure Azure App Service Settings](../app-service/configure-common.md)..

### <a name="app-service-editor"></a><a name="editor"></a>App Service szerkesztő

![A App Service szerkesztő](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

A App Service szerkesztő egy speciális portálon található szerkesztő, amely a JSON-konfigurációs fájlok és kódfájlok módosítására is használható. Ha ezt a lehetőséget választja, egy különálló böngészőlapot indít el egy alapszintű szerkesztővel. Ez lehetővé teszi a Git-adattárak integrálását, a kód futtatását és hibakeresését, valamint a függvényalkalmazás beállításainak módosítását. Ez a szerkesztő továbbfejlesztett fejlesztési környezetet biztosít a függvények számára a beépített függvényszerkesztővel összehasonlítva.  

Javasoljuk, hogy fontolja meg a függvények helyi számítógépen való fejlesztését. A helyi fejlesztés és az Azure-ban való közzététel során a projektfájlok csak olvashatók a portálon. További információ: Kód és tesztelés Azure Functions [helyileg.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Console

![Függvényalkalmazás konzolja](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

A portálon belüli konzol ideális fejlesztői eszköz, ha inkább a parancssorból szeretné használni a függvényalkalmazást. A gyakori parancsok közé tartozik a könyvtár- és fájl-létrehozás, a navigáció, valamint a kötegelt fájlok és parancsfájlok végrehajtása. 

Helyi fejlesztés esetén javasoljuk, hogy használja a Azure Functions Core Tools [és](functions-run-local.md) az [Azure CLI-t.]

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Speciális eszközök (Kudu)

![A Kudu konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Az alkalmazás speciális App Service (más néven Kudu) hozzáférést biztosítanak a függvényalkalmazás speciális felügyeleti funkcióihoz. A Kudu segítségével kezelheti a rendszeradatokat, az alkalmazásbeállításokat, a környezeti változókat, a webhelybővítményeket, a HTTP-fejléceket és a kiszolgálóváltozókat. A Kudu a **függvényalkalmazás** SCM-végpontjának tallózásával is elindíthatja, például: `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Üzembe helyezési központ

Ha a függvénykódok fejlesztéséhez és karbantartásához egy forrásvezérlő megoldást használ, a Deployment Center lehetővé teszi a forrásvezérlésből való buildet és üzembe helyezést. A projekt létrehozása és üzembe helyezése az Azure-ban történik a frissítések során. További információ: [Telepítési technológiák a Azure Functions.](functions-deployment-technologies.md)

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Eltérő eredetű erőforrások megosztása

Az ügyfélen a rosszindulatú kódvégrehajtás megakadályozása érdekében a modern böngészők blokkolják a webalkalmazások kérését egy külön tartományban futó erőforrásokra. [Az eredetközi erőforrás-megosztás (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lehetővé teszi, hogy a fejlécek deklarálják, mely eredetek hívhatnak `Access-Control-Allow-Origin` végpontokat a függvényalkalmazásban.

#### <a name="portal"></a>Portál

Amikor konfigurálja **az Engedélyezett eredetek** listát a függvényalkalmazáshoz, a fejléc automatikusan hozzáadódik a függvényalkalmazás HTTP-végpontjaitól kapott `Access-Control-Allow-Origin` válaszokhoz. 

![A függvényalkalmazás CORS-listájának konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

A helyettesítő karakter ( ) használata esetén a rendszer az `*` összes többi tartományt figyelmen kívül hagyja. 

Az [`az functionapp cors add`](/cli/azure/functionapp/cors#az_functionapp_cors_add) paranccsal adjon hozzá egy tartományt az engedélyezett forráslistához. Az alábbi példa hozzáadja a contoso.com tartományt:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Az [`az functionapp cors show`](/cli/azure/functionapp/cors#az_functionapp_cors_show) paranccsal listába sorolja az aktuálisan engedélyezett eredeteket.

### <a name="authentication"></a><a name="auth"></a>Hitelesítés

![Függvényalkalmazás hitelesítésének konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Ha a függvények HTTP-eseményindítót használnak, a hívások hitelesítését megkövetelheti. App Service támogatja Azure Active Directory közösségi szolgáltatókkal, például a Facebookkal, a Microsofttal és a Twittersel való hitelesítést és bejelentkezést. Az egyes hitelesítésszolgáltatók konfigurálásával kapcsolatos részletekért lásd a Azure App Service [áttekintését.](../app-service/overview-authentication-authorization.md) 


## <a name="next-steps"></a>Következő lépések

+ [A Azure App Service konfigurálása](../app-service/configure-common.md)
+ [Folyamatos üzembe helyezés Azure Functions](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure Portal]: https://portal.azure.com
