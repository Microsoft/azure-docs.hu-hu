---
title: Function app-beállítások konfigurálása az Azure-ban
description: Ismerje meg, hogyan konfigurálhatja az Azure Function app beállításait.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 746a97ecd9b0bdd676e70cca38edc75905e3e4bd
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936940"
---
# <a name="manage-your-function-app"></a>A Function alkalmazás kezelése 

Azure Functions a functions-alkalmazás biztosítja az egyes függvények végrehajtási környezetét. A Function app Behavior az adott Function app által üzemeltetett összes függvényre vonatkozik. A Function alkalmazás összes függvényének azonos [nyelvnek](supported-languages.md)kell lennie. 

A functions alkalmazásban az egyes függvények együtt vannak telepítve, és a méretezésük együtt történik. Az azonos Function alkalmazásban található összes függvény egy példányban, a Function alkalmazás méretezése szerint. 

A rendszer a kapcsolatok karakterláncait, környezeti változóit és egyéb beállításait külön definiálja az egyes functions-alkalmazásokhoz. A Function Apps-alkalmazások között megosztható összes adatnak külsőleg kell lennie a megőrzött tárolóban.

Ez a cikk bemutatja, hogyan konfigurálhatja és kezelheti a függvények alkalmazásait. 

> [!TIP]  
> Az [Azure CLI]használatával számos konfigurációs beállítás is kezelhető. 

## <a name="get-started-in-the-azure-portal"></a>Első lépések az Azure Portalon

1. A kezdéshez nyissa meg a [Azure Portal] , és jelentkezzen be az Azure-fiókjába. A portál felső részén található keresősáv mezőben adja meg a Function alkalmazás nevét, és válassza ki a listából. 

2. A bal oldali ablaktábla **Beállítások** területén válassza a **Konfigurálás** lehetőséget.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="A Function app áttekintése a Azure Portal":::

A függvény alkalmazásának áttekintés oldaláról, különösen az **[Alkalmazásbeállítások](#settings)** és a **[platform funkcióinak](#platform-features)** kezeléséhez minden szükséges információt megtalálhat.

## <a name="work-with-application-settings"></a><a name="settings"></a>Alkalmazás-beállítások használata

Az **Alkalmazásbeállítások** lapon megtarthatja a Function alkalmazás által használt beállításokat. Ezeket a beállításokat a rendszer titkosított formában tárolja, és a portál értékeinek megjelenítéséhez ki kell választania az **értékek megjelenítése** elemet. Az Alkalmazásbeállítások az Azure CLI használatával is elérhetők.

### <a name="portal"></a>Portál

Egy beállítás a portálon való hozzáadásához válassza az **új Alkalmazásbeállítás** lehetőséget, és adja hozzá az új kulcs-érték párokat.

![A Azure Portalban található Function app-beállítások.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

A [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) parancs az alkalmazás meglévő beállításait adja vissza, ahogy az alábbi példában is látható:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

A [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) parancs egy Alkalmazásbeállítás hozzáadására vagy frissítésére szolgál. A következő példa létrehoz egy beállítást egy nevű kulccsal `CUSTOM_FUNCTION_APP_SETTING` és egy értékkel `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>Alkalmazásbeállítások használata

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Amikor helyileg fejleszt egy Function-alkalmazást, a Project fájl local.settings.jsjában meg kell őriznie ezeknek az értékeknek a helyi másolatát. További információ: [helyi beállítások fájl](functions-run-local.md#local-settings-file).

## <a name="hosting-plan-type"></a>Üzemeltetési csomag típusa

Egy Function-alkalmazás létrehozásakor létre kell hoznia egy App Service üzemeltetési csomagot is, amelyben az alkalmazás fut. Egy csomaghoz egy vagy több Function-alkalmazás is tartozhat. A függvények funkcionalitása, skálázása és díjszabása a csomag típusától függ. További információkért tekintse meg a [Azure functions díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/functions/).

Meghatározhatja, hogy milyen típusú tervet használ a Function alkalmazás a Azure Portal vagy az Azure CLI vagy a Azure PowerShell API-k használatával. 

A következő értékek a csomag típusát jelzik:

| Csomag típusa | Portál | Azure CLI/PowerShell |
| --- | --- | --- |
| [Felhasználás](consumption-plan.md) | **Felhasználás** | `Dynamic` |
| [Prémium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedikált (App Service)](dedicated-plan.md) | Különböző | Különböző |

# <a name="portal"></a>[Portál](#tab/portal)

A Function app által használt csomag típusának meghatározásához tekintse meg a [Azure Portalban](https://portal.azure.com)található Function alkalmazás **Áttekintés** lapján **app Service terv** című részt. Az árképzési csomag megjelenítéséhez válassza ki a **app Service terv** nevét, majd a bal oldali ablaktáblán válassza a **Tulajdonságok** lehetőséget.

![Méretezési terv megtekintése a portálon](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Futtassa az alábbi Azure CLI-parancsot a üzemeltetési csomag típusának lekéréséhez:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

Az előző példában cserélje le `<RESOURCE_GROUP>` a és az `<FUNCTION_APP_NAME>` erőforráscsoportot az erőforráscsoport és a Function app Names (megfelelő) elemre. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa a következő Azure PowerShell parancsot a üzemeltetési csomag típusának beszerzéséhez:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
Az előző példában cserélje le `<RESOURCE_GROUP>` a és az `<FUNCTION_APP_NAME>` erőforráscsoportot az erőforráscsoport és a Function app Names (megfelelő) elemre. 

---


## <a name="platform-features"></a>Platform-funkciók

A alkalmazásban futtatott Function apps-t a Azure App Service platform tartja karban. Így a Function apps az Azure alapvető webes üzemeltetési platformjának legtöbb funkcióját elérheti. A bal oldali panelen elérheti a App Service platform számos funkcióját, amelyeket használhat a Function apps alkalmazásban. 

> [!NOTE]
> Nem minden App Service funkció érhető el, ha egy Function alkalmazás fut a használati üzemeltetési csomagon.

A cikk további részében a következő, a függvények számára hasznos App Service Azure Portal-funkciókra összpontosít:

+ [App Service szerkesztő](#editor)
+ [Console](#console)
+ [Speciális eszközök (kudu)](#kudu)
+ [Üzembe helyezési beállítások](#deployment)
+ [CORS](#cors)
+ [Hitelesítés](#auth)

További információ a App Service beállítások használatáról: [Azure app Service beállításainak konfigurálása](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>App Service szerkesztő

![A App Service szerkesztő](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

A App Service szerkesztő egy olyan speciális portál szerkesztő, amely a JSON konfigurációs fájlok és a fájlok fájljainak módosítására használható. Ha ezt a lehetőséget választja, egy külön böngésző fület indít egy alapszintű szerkesztővel. Ez lehetővé teszi a git-tárház, a futtatási és hibakeresési kód integrálását, valamint a Function app beállításainak módosítását. Ez a szerkesztő fejlett fejlesztési környezetet biztosít a függvények összevetéséhez a beépített függvény-szerkesztővel szemben.  

Javasoljuk, hogy vegye fontolóra a függvények fejlesztését a helyi számítógépen. Amikor helyben fejleszti és közzéteszi az Azure-ban, a projektfájlok csak olvashatók a portálon. További információ: [code and test Azure functions helyileg](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Console

![Function app Console](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

A portálon található konzol ideális fejlesztői eszköz, ha a parancssorból szeretné használni a Function alkalmazást. Gyakori parancsok például a címtár és a fájlok létrehozása és a Navigálás, valamint a Batch-fájlok és-parancsfájlok futtatása. 

Helyi fejlesztés esetén javasolt a [Azure functions Core Tools](functions-run-local.md) és az [Azure CLI]használata.

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Speciális eszközök (kudu)

![Kudu konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

A App Service speciális eszközei (más néven kudu) hozzáférést biztosítanak a Function alkalmazás speciális felügyeleti funkcióihoz. A kudu a rendszerinformációk, az Alkalmazásbeállítások, a környezeti változók, a helybővítmények, a HTTP-fejlécek és a kiszolgálói változók kezelése. A **kudu** a Function app SCM-végpontjának tallózásával is elindíthatja, például: `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Üzembe helyezési központ

Ha verziókövetés-megoldást használ a függvények kódjának fejlesztésére és karbantartására, a központi telepítési központ lehetővé teszi a forrás-vezérlőelemből való felépítést és üzembe helyezést. A projekt létrehozása és üzembe helyezése az Azure-ban történik, amikor frissítéseket végez. További információ: [Azure functions telepítési technológiák](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Eltérő eredetű erőforrások megosztása

A rosszindulatú programkódok végrehajtásának megakadályozása érdekében a modern böngészők letiltják a webalkalmazásokból érkező kéréseket egy különálló tartományban futó erőforrásoknak. A [több eredetű erőforrás-megosztás (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) lehetővé teszi `Access-Control-Allow-Origin` , hogy egy fejléc deklarálja, hogy mely eredetűek hívhatják meg a végpontokat a Function alkalmazásban.

#### <a name="portal"></a>Portál

Ha beállítja a Function alkalmazás számára **engedélyezett Origins** -listát, a `Access-Control-Allow-Origin` rendszer automatikusan hozzáadja a fejlécet a Function alkalmazásban lévő http-végpontok összes válaszához. 

![Function alkalmazás CORS-listájának konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Ha a helyettesítő karaktert ( `*` ) használja, az összes többi tartományt figyelmen kívül hagyja a rendszer. 

A [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) parancs használatával adjon hozzá egy tartományt az engedélyezett Origins listához. A következő példa hozzáadja a contoso.com tartományt:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

A [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) parancs használatával listázhatja az aktuálisan engedélyezett eredeteket.

### <a name="authentication"></a><a name="auth"></a>Hitelesítés

![Egy Function alkalmazás hitelesítésének konfigurálása](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Ha a függvények HTTP-triggert használnak, megkövetelheti, hogy először hitelesíteni lehessen a hívásokat. App Service támogatja a Azure Active Directory hitelesítését és a közösségi szolgáltatókkal való bejelentkezést, például a Facebookot, a Microsoftot és a Twittert. Az egyes hitelesítő szolgáltatók konfigurálásával kapcsolatos részletekért tekintse meg a [Azure app Service Authentication áttekintése](../app-service/overview-authentication-authorization.md)című témakört. 


## <a name="next-steps"></a>További lépések

+ [Azure App Service beállítások konfigurálása](../app-service/configure-common.md)
+ [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure Portal]: https://portal.azure.com
