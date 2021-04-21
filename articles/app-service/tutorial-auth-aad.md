---
title: 'Oktatóanyag: Felhasználók hitelesítése E2E-ben'
description: Megtudhatja, hogyan használhatja App Service hitelesítést és engedélyezést a App Service alkalmazások teljes biztonságának biztosítása érdekében, beleértve a távoli API-khoz való hozzáférést is.
keywords: app service, azure app service, authN, authZ, védelem, biztonság, többrétegű, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: e35a4abbcaf0cd2298d55c06de0ecfbef0dff354
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765790"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Oktatóanyag: Felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service-ben

::: zone pivot="platform-windows"  

Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Az App Service továbbá beépített támogatást nyújt a [felhasználók hitelesítéséhez és engedélyezéséhez](overview-authentication-authorization.md). Ebből az oktatóanyagból megtudhatja, hogyan gondoskodhat az alkalmazások védelméről az App Service-hitelesítés és -engedélyezés segítségével. Egy ASP.NET Core-alkalmazást használ, Angular.js előtétet használ példaként. Az App Service-hitelesítés és -engedélyezés támogatja az összes nyelvi futtatókörnyezetet, Ön pedig az oktatóanyag elvégzésével megismerheti, hogyan alkalmazhatja ezt a kívánt nyelvre.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) egy nagymértékben skálázható, önjavítást használó webes üzemeltetési szolgáltatást nyújt a Linux operációs rendszer használatával. Az App Service továbbá beépített támogatást nyújt a [felhasználók hitelesítéséhez és engedélyezéséhez](overview-authentication-authorization.md). Ebből az oktatóanyagból megtudhatja, hogyan gondoskodhat az alkalmazások védelméről az App Service-hitelesítés és -engedélyezés segítségével. Ez egy ASP.NET Core-alkalmazást használ, Angular.js előtétet használ példaként. Az App Service-hitelesítés és -engedélyezés támogatja az összes nyelvi futtatókörnyezetet, Ön pedig az oktatóanyag elvégzésével megismerheti, hogyan alkalmazhatja ezt a kívánt nyelvre.

::: zone-end

![Egyszerű hitelesítés és engedélyezés](./media/tutorial-auth-aad/simple-auth.png)

Azt is bemutatja, hogyan gondoskodhat egy többrétegű alkalmazás védelméről egy védett háttérrendszeri API a hitelesített felhasználó nevében történő elérésével mind a [kiszolgálói kódból](#call-api-securely-from-server-code), mind a [böngészőkódból](#call-api-securely-from-browser-code).

![Speciális hitelesítés és engedélyezés](./media/tutorial-auth-aad/advanced-auth.png)

Ez mindössze néhány az App Service-ben elérhető összes lehetséges hitelesítési és engedélyezési eljárás közül. 

Az alábbi lista az oktatóanyagban ismertetett témaköröket tartalmazza:

> [!div class="checklist"]
> * Beépített hitelesítés és engedélyezés engedélyezése
> * Alkalmazások biztonságossá tétele a nem hitelesített kérelmek ellen
> * Az Azure Active Directory használata identitásszolgáltatóként
> * Távoli alkalmazás elérése a bejelentkezett felhasználó nevében
> * Biztonságos szolgáltatások közötti hívások jogkivonat-hitelesítéssel
> * Hozzáférési kódok használata kiszolgálói kódból
> * Hozzáférési kódok használata az ügyfél (böngésző) kódjából

Az oktatóanyag lépései macOS, Linux és Windows rendszerre is vonatkoznak.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">A legújabb .NET Core 3.1 SDK telepítése</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Helyi .NET Core-alkalmazás létrehozása

Ebben a lépésben a helyi .NET Core-projektet állíthatja be. Ugyanezen projekt használatával telepít egy háttérrendszeri API-alkalmazást és egy előtér-webalkalmazást.

### <a name="clone-and-run-the-sample-application"></a>A mintaalkalmazás klónozása és futtatása

Az alábbi parancsok futtatásával klónozza, majd futtassa a mintaadattárat.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Navigáljon ide: `http://localhost:5000`, majd adja hozzá, szerkessze vagy távolítsa el a teendőket. 

![Helyileg futó ASP.NET Core API](./media/tutorial-auth-aad/local-run.png)

Ha bármikor le szeretné állítani az ASP.NET Core-t, nyomja le a `Ctrl+C` billentyűkombinációt a terminálon.

## <a name="deploy-apps-to-azure"></a>Alkalmazások telepítése az Azure-ba

Ebben a lépésben üzembe helyezi a projektet két App Service-alkalmazásban. Az egyik az előtéri, a másik a háttéralkalmazás.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure-erőforrások létrehozása

::: zone pivot="platform-windows"  

A Cloud Shell futtassa a következő parancsokat két Windows-webalkalmazás létrehozásához. Cserélje le a és a karaktereket két globálisan _\<front-end-app-name>_ _\<back-end-app-name>_ egyedi alkalmazásnévre (érvényes karakterek: `a-z` , és `0-9` `-` ). A parancsokról további információt talál a [CORS-támogatással rendelkező RESTful API üzemeltetése az Azure App Service-ben](app-service-web-tutorial-rest-api.md) című szakaszban.

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

A Cloud Shellben futtassa a következő parancsokat két webalkalmazás létrehozásához. Cserélje le a és a karaktereket két globálisan _\<front-end-app-name>_ _\<back-end-app-name>_ egyedi alkalmazásnévre (érvényes karakterek: `a-z` , és `0-9` `-` ). Az egyes parancsokkal kapcsolatos további információkért [lásd: .NET Core-alkalmazás létrehozása a Azure App Service.](quickstart-dotnetcore.md)

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> Mentse a távoli Git-elemek URL-címeit az előtér- és a háttéralkalmazás esetében. Ezek az `az webapp create` kimenetében láthatók.
>

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

A _helyi terminálablakba_ visszalépve futtassa a következő Git-parancsokat a háttéralkalmazás telepítéséhez. Cserélje _\<deploymentLocalGitUrl-of-back-end-app>_ le a helyére a távoli Git-erőforrás URL-címét, amely az [Azure-erőforrások létrehozása alatt mentve van.](#create-azure-resources) Amikor a Git Hitelesítőadat-kezelő kéri a hitelesítő adatokat, ügyeljen arra, hogy az üzembe helyezési hitelesítő adatokat adja [meg,](deploy-configure-credentials.md)ne pedig a hitelesítő adatokat, amelyet a Azure Portal.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

A helyi terminálablakban futtassa a következő Git-parancsokat ugyanezen kódnak az előtér-alkalmazásba történő telepítéséhez. Cserélje _\<deploymentLocalGitUrl-of-front-end-app>_ le a helyére a távoli Git-erőforrás URL-címét, amely az [Azure-erőforrások létrehozása alatt mentve van.](#create-azure-resources)

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Az alkalmazások tallózása

Nyissa meg a következő URL-címeket egy böngészőben, és ellenőrizze a két alkalmazás működését.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="Képernyőkép egy Azure App Service REST API-mintáról egy böngészőablakban, amely egy To do list alkalmazást mutat be.":::

> [!NOTE]
> Ha az alkalmazás újraindul, láthatja, hogy az új adatok törölve lettek. Ez az elvárt működés, ugyanis az ASP.NET Core mintaalkalmazás memóriában lévő adatbázist használ.
>
>

## <a name="call-back-end-api-from-front-end"></a>Háttérrendszeri API meghívása az előtérből

Ebben a lépésben kijelöli az előtér-alkalmazás kiszolgálói kódját, hogy hozzáférjen a háttérrendszeri API-hoz. Később engedélyezni fogja az előtértől a háttérbe irányuló hitelesített hozzáférést.

### <a name="modify-front-end-code"></a>Az előtérkód módosítása

A helyi adattárban nyissa meg a következőt: _Controllers/TodoController.cs_. A osztály elején adja hozzá a következő sorokat, és cserélje le a `TodoController` _\<back-end-app-name>_ helyére a háttéralkalmazás nevét:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Keresse meg a metódust, amely a következővel van helyettesítve, és cserélje le a kapcsos zárójelek között lévő kódot a `[HttpGet]` következőre:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

Az első sor `GET /api/Todo` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezután keresse meg a metódust, amely a következővel van helyettesítve, és cserélje le a kapcsos zárójelek között lévő kódot a `[HttpGet("{id}")]` következőre:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

Az első sor `GET /api/Todo/{id}` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezután keresse meg a metódust, amely a következővel van helyettesítve, és cserélje le a kapcsos zárójelek között lévő kódot a `[HttpPost]` következőre:

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

Az első sor `POST /api/Todo` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezután keresse meg a metódust, amely a következővel van helyettesítve, és cserélje le a kapcsos zárójelek között lévő kódot a `[HttpPut("{id}")]` következőre:

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

Az első sor `PUT /api/Todo/{id}` hívást indít a háttérrendszeri API-alkalmazás felé.

Ezután keresse meg a metódust, amely a következővel van ékezetve, és cserélje le a kapcsos zárójelek között lévő kódot a `[HttpDelete("{id}")]` következőre:

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

Az első sor `DELETE /api/Todo/{id}` hívást indít a háttérrendszeri API-alkalmazás felé.

Mentse az összes módosítást. A helyi terminálablakból telepítse a módosításokat az előtér-alkalmazásban a következő Git-parancsok segítségével:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>A módosítások ellenőrzése

Nyissa meg a `http://<front-end-app-name>.azurewebsites.net` címet, és adjon hozzá néhány elemet, például a `from front end 1` és `from front end 2` elemet.

Nyissa meg a `http://<back-end-app-name>.azurewebsites.net` címet az előtér-alkalmazásból hozzáadott elemek megtekintéséhez. Továbbá adjon hozzá néhány elemet, például a `from back end 1` és a `from back end 2` elemet, majd frissítse az előtér-alkalmazást annak ellenőrzéséhez, hogy az tükrözi-e a módosításokat.

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="Képernyőkép egy Azure App Service REST API-mintáról egy böngészőablakban, amely egy To do list (To do list) alkalmazást mutat az előoldali alkalmazásból hozzáadott elemekkel.":::

## <a name="configure-auth"></a>Hitelesítés konfigurálása

Ebben a lépésben engedélyezni fogja a két alkalmazás hitelesítését és engedélyezését. Konfigurálni fogja továbbá az előtér-alkalmazást, hogy létrehozzon egy hozzáférési jogkivonatot, amelynek segítségével hitelesített hívásokat indíthat a háttéralkalmazáshoz.

Az Azure Active Directoryt fogja használni identitásszolgáltatóként. További információért tekintse meg [az Azure Active Directory-hitelesítés az App Services-alkalmazáshoz való konfigurálásáról](configure-authentication-provider-aad.md) szóló részt.

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Hitelesítés és engedélyezés engedélyezése a háttéralkalmazás számára

A [Azure Portal](https://portal.azure.com) válassza az **Erőforráscsoportok** lehetőséget, vagy keresse meg és válassza ki az *Erőforráscsoportok* elemet bármelyik oldalon.

Az **Erőforráscsoportok mezőben** keresse meg és válassza ki az erőforráscsoportot. Az **Áttekintés lapon** válassza ki a háttéralkalmazás felügyeleti oldalát.

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Képernyőkép az Erőforráscsoportok ablakról, amely egy példa erőforráscsoport Áttekintés lapját és egy háttéralkalmazás felügyeleti oldalát mutatja be.":::

A háttéralkalmazás bal oldali menüjében válassza a Hitelesítés **/Engedélyezés** lehetőséget, majd engedélyezze App Service hitelesítést a Be lehetőség **kiválasztásával.**

Az **Elvégzendő művelet, ha a kérés nincs hitelesítve** területen válassza a **Bejelentkezés az Azure Active Directoryval** lehetőséget.

A **Hitelesítésszolgáltatók alatt** válassza **a** Azure Active Directory.

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="A háttéralkalmazás bal oldali menüjének képernyőképe, amely a hitelesítés/engedélyezés lehetőséget és a jobb oldali menüben kiválasztott beállításokat mutatja.":::

Válassza **az Expressz** lehetőséget, fogadja el az alapértelmezett beállításokat egy új AD-alkalmazás létrehozásához, majd kattintson az **OK gombra.**

A Hitelesítés **/Engedélyezés lapon** válassza a Mentés **lehetőséget.**

Ha megjelenik az értesítés az `Successfully saved the Auth Settings for <back-end-app-name> App` üzenettel, frissítse a portáloldalt.

Válassza **Azure Active Directory** lehetőséget, majd válassza ki a **Azure AD alkalmazás.**

Másolja **az** Azure AD-alkalmazás ügyfél-azonosítóját egy jegyzettömbbe. Erre az értékre később szüksége lesz.

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Képernyőkép a Azure Active Directory beállítások ablakról, amely a Azure AD alkalmazás és az Azure AD-alkalmazások ablakát mutatja a másolt ügyfél-azonosítóval.":::

Ha itt megáll, egy olyan önálló alkalmazással rendelkezik, amelyet már biztonságos a App Service hitelesítés és engedélyezés. A fennmaradó szakaszok azt mutatják be, hogyan biztosíthatja a többalkalmazásos megoldások biztonságát a hitelesített felhasználónak az előtere és a háttér között való "áramlásával". 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Hitelesítés és engedélyezés engedélyezése az előtér-alkalmazás számára

Kövesse újból a lépéseket az előtér-alkalmazás esetében is, de az utolsó lépést hagyja ki. Nincs szükség az előoldali alkalmazás ügyfél-azonosítójára.

Igény szerint navigáljon a következőhöz: `http://<front-end-app-name>.azurewebsites.net`. Ez egy biztonságos bejelentkezési oldalra irányítja át. Miután bejelentkezik, továbbra sem férhet hozzá az adatokhoz a háttéralkalmazásból, mivel a háttéralkalmazáshoz Azure Active Directory előoldali alkalmazásból való bejelentkezés szükséges. Három dolgot kell megtennie:

- Hozzáférés engedélyezése az előtér-alkalmazás számára a háttéralkalmazáshoz
- Az App Service konfigurálása használható jogkivonat visszaadására
- A jogkivonat használata a kódban

> [!TIP]
> Ha hibákba ütközik, és újrakonfigurálja az alkalmazás hitelesítési/engedélyezési beállításait, előfordulhat, hogy a jogkivonat-tároló nem hozza létre újból a jogkivonatokat az új beállításokból. A jogkivonatok újbóli létrehozásának biztosításához ki kell jelentkeznie, majd újból be kell jelentkeznie az alkalmazásba. Ennek egyszerű módja, hogy a böngészőt privát üzemmódban használja, és bezárja, majd újból megnyitja a böngészőt privát üzemmódban az alkalmazások beállításainak módosítása után.

### <a name="grant-front-end-app-access-to-back-end"></a>Hozzáférés engedélyezése az előtér-alkalmazás számára a háttéralkalmazáshoz

Most, hogy engedélyezve van a hitelesítés és az engedélyezés mindkét alkalmazás számára, mindkét alkalmazás AD-alkalmazáson alapul. Ebben a lépésben engedélyeket ad az előtér-alkalmazásnak, hogy hozzáférhessen a háttéralkalmazáshoz a felhasználó nevében. (Voltaképpen az előtér _AD-alkalmazásának_ ad engedélyt a háttér _AD-alkalmazásához_ való hozzáférésre a felhasználó nevében.)

A [Azure Portal](https://portal.azure.com) menüben válassza **a** Azure Active Directory vagy a keresés lehetőséget, majd válassza Azure Active Directory *bármelyik* oldalon.

Válassza **Alkalmazásregisztrációk**  >  **a**  >  **könyvtárban Az összes megtekintése alkalmazásokhoz használt saját alkalmazásokat.** Válassza ki az előoldali alkalmazás nevét, majd válassza az **API-engedélyek lehetőséget.**

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="Képernyőkép a Microsoft Alkalmazásregisztrációk ablakról, a kiválasztott Saját alkalmazások, előoldali alkalmazásnév és API-engedélyek lehetőségével.":::

Válassza **az Engedély hozzáadása lehetőséget,** majd válassza ki a szervezet által használt **API-kat.**  >  **\<back-end-app-name>**

A **háttéralkalmazás API-engedélyek** kérése lapján válassza  a Delegált engedélyek lehetőséget, majd válassza **user_impersonation** engedélyek **hozzáadása lehetőséget.**

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="A Request API permissions (API-engedélyek kérése) lap képernyőképe, melyen a Delegált engedélyek, a user_impersonation és a Kijelölt Engedély hozzáadása gomb látható.":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Az App Service konfigurálása használható hozzáférési jogkivonat visszaadására

Az előoldali alkalmazás most már rendelkezik a szükséges engedélyekkel ahhoz, hogy bejelentkezett felhasználóként hozzáférjen a háttéralkalmazáshoz. Ebben a lépésben konfigurálja az App Service-hitelesítést és -engedélyezést, hogy használható jogkivonatot adjon a háttéralkalmazás eléréséhez. Ehhez a lépéshez szüksége lesz a háttéralkalmazás ügyfél-azonosítójára, amelyet a Hitelesítés és engedélyezés engedélyezése a háttéralkalmazáshoz lehetőségből [másolt ki.](#enable-authentication-and-authorization-for-back-end-app)

Lépjen a [Azure Erőforrás-kezelő,](https://resources.azure.com) és az erőforrásfával keresse meg az előoldali webalkalmazást.

A [Azure Erőforrás-kezelő](https://resources.azure.com) az erőforrásfában kiválasztott előoldali alkalmazással nyílik meg. Kattintson az **Olvasás/Írás** elemre a lap tetején az Azure-erőforrások szerkesztésének engedélyezéséhez.

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="Képernyőkép a lap tetején található Csak olvasási és olvasási/írási/olvasási Azure Erőforrás-kezelő, az Olvasás/Írás gomb kijelölve.":::

A bal oldali böngészőben részletezés a **config**  >  **authsettings beállításhoz.**

Az **authsettings** nézetben kattintson a **Szerkesztés** gombra. Állítsa `additionalLoginParams` a következő JSON-sztringre a másolt ügyfél-azonosítóval. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="Képernyőkép egy példakódról az authsettings nézetben, amely az additionalLoginParams sztringet mutatja egy példa ügyfél-azonosítóval.":::

A beállítások mentéséhez kattintson a **PUT** elemre.

Az alkalmazások konfigurálása kész. Az előtér-alkalmazás most már hozzáfér a háttéralkalmazáshoz a megfelelő hozzáférési jogkivonattal.

További információ a hozzáférési jogkivonat más szolgáltatók számára való konfigurálásával kapcsolatban: [Identitásszolgáltatói jogkivonatok frissítése.](app-service-authentication-how-to.md#refresh-identity-provider-tokens)

## <a name="call-api-securely-from-server-code"></a>API biztonságos hívása a kiszolgálói kódból

Ebben a lépésben engedélyezi a korábban módosított kiszolgálói kód számára, hogy hitelesített hívásokat indítson a háttérrendszeri API irányába.

Az előterealkalmazás most már rendelkezik a szükséges engedéllyel, és hozzáadja a háttéralkalmazás ügyfél-azonosítóját a bejelentkezési paraméterekhez. Ezáltal hozzáférési jogkivonatot szerezhet a háttéralkalmazással történő hitelesítéshez. Az App Service úgy adja meg a jogkivonatot a kiszolgálói kódhoz, hogy injektál egy `X-MS-TOKEN-AAD-ACCESS-TOKEN` fejlécet minden hitelesített kéréshez (lásd az [alkalmazáskódban lévő jogkivonatok lekéréséről](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) szóló részt).

> [!NOTE]
> Ezeket a fejléceket a rendszer minden támogatott nyelven injektálja. Az egyes nyelvek szokásos mintájának használatával férhet hozzájuk.

A helyi adattárban nyissa meg újból a következőt: _Controllers/TodoController.cs_. A `TodoController(TodoContext context)` konstruktorhoz adja hozzá a következő kódot:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Ez a kód hozzáadja a szabványos `Authorization: Bearer <access-token>` HTTP-fejlécet az összes távoli API-híváshoz. A ASP.NET MVC-kérelemvégrehajtási folyamatban a rendszer a megfelelő művelet végrehajtása előtt hajtja végre a műveletet, így mostantól minden kimenő API-hívás bemutatja a `OnActionExecuting` hozzáférési jogkivonatot.

Mentse az összes módosítást. A helyi terminálablakból telepítse a módosításokat az előtér-alkalmazásban a következő Git-parancsok segítségével:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Jelentkezzen be újra az `https://<front-end-app-name>.azurewebsites.net` helyre. A felhasználói adatok használatára vonatkozó megállapodás lapján kattintson az **Elfogadás** gombra.

Ekkor már képesnek kell lennie az adatok létrehozására, olvasására, frissítésére és törlésére a háttéralkalmazásból, mint korábban. Az egyetlen különbség, hogy az App Service hitelesítése és engedélyezése most már mindkét alkalmazást védi, beleértve a szolgáltatások közötti hívásokat is.

Gratulálunk! A kiszolgálói kód most már hozzáfér a háttéralkalmazás adataihoz a hitelesített felhasználó nevében.

## <a name="call-api-securely-from-browser-code"></a>API biztonságos hívása a böngészőkódból

Ebben a lépésben az előtérrendszeri Angular.js alkalmazást a háttérrendszeri API-hoz fogja irányítani. Ezáltal megtudhatja, hogyan kérheti le a hozzáférési jogkivonatot, és hogyan indíthat azzal API-hívásokat a háttéralkalmazás felé.

Míg a kiszolgálói kód hozzáfér a kérelemfejlécekhez, az ügyfélkód ugyanezeket a hozzáférési jogkivonatokat a következőből érheti el: `GET /.auth/me` (lásd az [alkalmazáskódban lévő jogkivonatok lekéréséről](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) szóló részt).

> [!TIP]
> Ez a szakasz a szabványos HTTP-metódusok használatával mutatja be a biztonságos HTTP-hívásokat. A [JavaScripthez](https://github.com/AzureAD/microsoft-authentication-library-for-js) használható Microsoft Authentication Library használatával azonban leegyszerűsítheti a Angular.js mintáját.
>

### <a name="configure-cors"></a>A CORS konfigurálása

A Cloud Shell az paranccsal engedélyezze a CORS-t az ügyfél [`az webapp cors add`](/cli/azure/webapp/cors#az_webapp_cors_add) URL-címére. Cserélje le a _\<back-end-app-name>_ és _\<front-end-app-name>_ a helyőrzőket.

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Ez a lépés nem kapcsolódik a hitelesítéshez és az engedélyezéshez. Azonban szükség van rá ahhoz, hogy a böngésző lehetővé tegye a tartományok közötti API-hívásokat az Angular.js-alkalmazásból. További információ: [CORS-funkció hozzáadása](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js-alkalmazás átirányítása a háttérrendszeri API felé

A helyi adattárban nyissa meg a következőt: _wwwroot/index.html_.

Az 51. sorban állítsa a változót a háttéralkalmazás `apiEndpoint` HTTPS URL-címére ( `https://<back-end-app-name>.azurewebsites.net` ). Cserélje _\<back-end-app-name>_ le a helyére az alkalmazás nevét a App Service.

Nyissa meg a helyi adattárban a _wwwroot/app/scripts/todoListSvc.js_ fájlt, és ellenőrizze, hogy minden API-hívás a következővel van-e kiegészítve: `apiEndpoint`. Az Angular.js-alkalmazás most már a háttérrendszer API-kat fogja hívni. 

### <a name="add-access-token-to-api-calls"></a>Hozzáférési jogkivonat hozzáadása az API-hívásokhoz

A _wwwroot/app/scripts/todoListSvc.js_ fájlban az API-hívások listája (`getItems : function(){`. sor) fölött adja hozzá a következő függvényt a listához:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

A rendszer ezt a függvényt fogja meghívni az alapértelmezett `Authorization` fejléc a hozzáférési jogkivonattal történő beállításához. A következő lépésben fogja meghívni.

A helyi adattárban nyissa meg a _wwwroot/app/scripts/app.js_ fájlt, és keresse meg a következő kódot:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Cserélje le a teljes kódblokkot az alábbi kódra:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Az új módosítás hozzáadja a `resolve` leképezést, amely a `/.auth/me` meghívása után beállítja a hozzáférési jogkivonatot. Ellenőrzi, hogy rendelkezik-e a hozzáférési jogkivonattal, mielőtt létrehozza a `todoListCtrl` vezérlő példányát. E módon minden, a vezérlő által kezdeményezett API-hívás tartalmazni fogja a jogkivonatot.

### <a name="deploy-updates-and-test"></a>Frissítések telepítése és tesztelése

Mentse az összes módosítást. A helyi terminálablakból telepítse a módosításokat az előtér-alkalmazásban a következő Git-parancsok segítségével:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Keresse meg újból a következőt: `https://<front-end-app-name>.azurewebsites.net`. Most már képesnek kell lennie az adatok közvetlenül az Angular.js-alkalmazásból történő létrehozására, olvasására, frissítésére és törlésére.

Gratulálunk! Az ügyfélkód most már hozzáfér a háttéradatokhoz a hitelesített felhasználó nevében.

## <a name="when-access-tokens-expire"></a>A hozzáférési jogkivonatok lejártakor

A hozzáférési jogkivonatok bizonyos idő elteltével lejárnak. További információ a hozzáférési jogkivonatok frissítésére anélkül, hogy a felhasználóknak újrahitelesíteték volna az alkalmazást: [Identitásszolgáltatói jogkivonatok frissítése.](app-service-authentication-how-to.md#refresh-identity-provider-tokens)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben Azure-erőforrásokat hozott létre egy erőforráscsoportban. Ha várhatóan nem lesz szüksége ezekre az erőforrásokra a jövőben, törölje az erőforráscsoportot a következő parancs Cloud Shellben történő futtatásával:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

A parancs futtatása egy percig is eltarthat.

<a name="next"></a>
## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * Beépített hitelesítés és engedélyezés engedélyezése
> * Alkalmazások biztonságossá tétele a nem hitelesített kérelmek ellen
> * Az Azure Active Directory használata identitásszolgáltatóként
> * Távoli alkalmazás elérése a bejelentkezett felhasználó nevében
> * Biztonságos szolgáltatások közötti hívások jogkivonat-hitelesítéssel
> * Hozzáférési kódok használata kiszolgálói kódból
> * Hozzáférési kódok használata az ügyfél (böngésző) kódjából

A következő oktatóanyag azt is bemutatja, hogyan lehet leképezni egy egyéni DNS-nevet az alkalmazásra.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név leképezése a Azure App Service](app-service-web-tutorial-custom-domain.md)
