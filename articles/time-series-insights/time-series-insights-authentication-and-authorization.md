---
title: API-hitelesítés és-engedélyezés – Azure Time Series Insights | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hitelesítés és engedélyezés egy olyan egyéni alkalmazáshoz, amely meghívja a Azure Time Series Insights API-t.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 02d9edd555566f86fd8bb09cf4acef4956ae53e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041212"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz

Az üzleti igényektől függően előfordulhat, hogy a megoldás egy vagy több ügyfélalkalmazások közé tartozik, amelyet a Azure Time Series Insights-környezet [API-jai](/rest/api/time-series-insights/reference-data-access-overview)segítségével szeretne kezelni. [A Azure Time Series Insights az Azure ad biztonsági jogkivonatok használatával hajtja végre a hitelesítést az OAUTH 2,0 alapján](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims). Az ügyfél (ek) hitelesítéséhez be kell szereznie a megfelelő engedélyekkel rendelkező tulajdonosi jogkivonatot, és át kell adnia az API-hívásokkal. Ez a dokumentum több hitelesítő adat beszerzésére szolgáló módszert ismertet, amelyek segítségével tulajdonosi jogkivonatot és hitelesítést végezhet.


  alkalmazások regisztrálása Azure Active Directory az új Azure Active Directory panel használatával. A Azure Active Directory regisztrált alkalmazások lehetővé teszik a felhasználók számára, hogy hitelesítsék magukat a Azure Time Series Insights-környezethez társított Azure Time Series Insight API használatával.

## <a name="managed-identities"></a>Felügyelt identitások

A következő szakaszok azt ismertetik, hogyan lehet felügyelt identitást használni a Azure Active Directory (Azure AD) szolgáltatásból a Azure Time Series Insights API eléréséhez. Az Azure-ban a felügyelt identitások nem teszik lehetővé, hogy a fejlesztők a hitelesítő adatok kezeléséhez a személyazonosságot az Azure AD-ben, az Azure AD-ben pedig Azure Active Directory (Azure AD-) tokenek beszerzéséhez használják. A felügyelt identitások használatának előnyei a következők:

- Nem kell kezelnie a hitelesítő adatokat. A hitelesítő adatok még nem érhetők el.
- A felügyelt identitások használatával bármely olyan Azure-szolgáltatáshoz hitelesíthető, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vaultt is.
- A felügyelt identitások további díjak nélkül is használhatók.

A felügyelt identitások két típusával kapcsolatos további információkért olvassa el az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) című témakört.

A felügyelt identitások a következővel használhatók:

- Azure-beli virtuális gépek
- Azure App Services
- Azure Functions
- Azure Container instances
- és még sok minden más...

Tekintse meg az Azure- [erőforrások felügyelt identitásokat támogató Azure-szolgáltatásait](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) a teljes listához.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory alkalmazás regisztrálása

Ajánlott a felügyelt identitások használata, amikor csak lehetséges, így nem szükséges a hitelesítő adatok kezelése. Ha az ügyfélalkalmazás nem olyan Azure-szolgáltatáson fut, amely támogatja a felügyelt identitásokat, regisztrálhatja alkalmazását egy Azure AD-Bérlővel. Ha az Azure AD-vel regisztrálja az alkalmazást, az alkalmazáshoz olyan identitás-konfigurációt hoz létre, amely lehetővé teszi, hogy integrálható legyen az Azure AD-vel. Ha a [Azure Portalban](https://portal.azure.com/)regisztrál egy alkalmazást, kiválaszthatja, hogy egyetlen bérlő (csak a bérlőben érhető el) vagy több-bérlős (más bérlők számára elérhető), és opcionálisan beállíthat-e ÁTirányítási URI-t (ahol a hozzáférési jogkivonat el van küldve).

Az alkalmazás regisztrációjának befejezése után az alkalmazás (az Application objektum) globálisan egyedi példánya van, amely a saját bérlőn vagy címtáron belül él. Az alkalmazáshoz (az alkalmazáshoz vagy az ügyfél-AZONOSÍTÓhoz) globálisan egyedi azonosítót is használhat. A portálon titkokat vagy tanúsítványokat és hatóköröket adhat hozzá az alkalmazás működéséhez, testreszabhatja az alkalmazás arculatát a bejelentkezési párbeszédpanelen, és így tovább.

Ha regisztrál egy alkalmazást a portálon, a rendszer automatikusan létrehoz egy Application-objektumot és egy egyszerű szolgáltatásnevet a saját bérlőben. Ha a Microsoft Graph API-k használatával regisztrál/hoz létre alkalmazást, az egyszerű szolgáltatás létrehozása egy külön lépés. A jogkivonatok igényléséhez egyszerű szolgáltatásnév-objektum szükséges.

Mindenképpen tekintse át az alkalmazás [biztonsági](../active-directory/develop/identity-platform-integration-checklist.md#security) ellenőrzőlistáját. Ajánlott eljárásként a [tanúsítvány hitelesítő adatait](../active-directory/develop/active-directory-certificate-credentials.md)kell használnia, nem pedig a jelszó hitelesítő adatait (az ügyfél titkos kulcsait).

További részletekért lásd: [alkalmazás-és szolgáltatásnév-objektumok Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) .

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>1. lépés: a felügyelt identitás vagy alkalmazás regisztrációjának létrehozása

Miután azonosította, hogy felügyelt identitást vagy alkalmazás-regisztrációt használ-e, a következő lépés egy kiépítés.

### <a name="managed-identity"></a>Felügyelt identitás

A felügyelt identitás létrehozásához szükséges lépések eltérőek lehetnek attól függően, hogy hol található a kód, és hogy van-e létrehozva rendszerhez rendelt vagy felhasználó által hozzárendelt identitás. A különbség megismeréséhez olvassa el a [felügyelt identitások típusait](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) . Miután kiválasztotta az identitás típusát, keresse meg és kövesse a megfelelő oktatóanyagot az Azure AD által felügyelt identitások [dokumentációjában](../active-directory/managed-identities-azure-resources/index.yml). Itt útmutatást talál a felügyelt identitások konfigurálásához a következőhöz:

- [Azure-beli virtuális gépek](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service és Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- és még sok minden más...

### <a name="application-registration"></a>Alkalmazásregisztráció

Kövesse az [alkalmazás regisztrálása](../active-directory/develop/quickstart-register-app.md#register-an-application)című témakörben felsorolt lépéseket.

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>2. lépés: hozzáférés engedélyezése

Ha a Azure Time Series Insights-környezet kérést kap, először a hívó tulajdonosi jogkivonatát kell érvényesíteni. Ha az érvényesítés sikeres, a hívó hitelesítése megtörtént, majd egy másik ellenőrzéssel biztosítható, hogy a hívó jogosult legyen a kért művelet végrehajtására. Bármely felhasználó vagy szolgáltatásnév engedélyezéséhez először hozzáférést kell biztosítania számukra a környezethez az olvasó vagy közreműködő szerepkör hozzárendelésével.

- A [Azure Portal](https://portal.azure.com/) felhasználói felületen keresztüli hozzáférés biztosításához kövesse az [adathozzáférés a környezethez](concepts-access-policies.md) című cikkben felsorolt utasításokat. A felhasználó kiválasztásakor a név vagy azonosító alapján keresheti meg a felügyelt identitást vagy az alkalmazás regisztrációját.

- Az Azure CLI használatával történő hozzáférés biztosításához futtassa a következő parancsot. Tekintse át az [itt](/cli/azure/ext/timeseriesinsights/tsi/access-policy) található dokumentációt a hozzáférések kezeléséhez elérhető parancsok teljes listájához.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Az Azure CLI-hez készült timeseriesinsights-bővítményhez a 2.11.0 vagy újabb verzió szükséges. A bővítmény automatikusan telepíti az az ÁME Access-Policy parancs első indításakor. [További](/cli/azure/azure-cli-extensions-overview) információ a bővítményekről.

## <a name="step-3-requesting-tokens"></a>3. lépés: jogkivonatok kérése

Miután a felügyelt identitása vagy az alkalmazás regisztrálása megtörtént, és hozzárendelt egy szerepkört, készen áll arra, hogy megkezdje a OAuth 2,0 tulajdonosi jogkivonatok igénylését. A token beszerzéséhez használt módszer eltérő lehet attól függően, hogy hol található a kód, és milyen nyelven választhat. Ha az erőforrást (más néven a token célközönségét) adja meg, akkor a Azure Time Series Insights az URL-cím vagy a GUID azonosító alapján azonosíthatja:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Ha az URL-címet erőforrás-AZONOSÍTÓként használja, a jogkivonatot pontosan a következőre kell kiadnia: `https://api.timeseries.azure.com/` . A záró perjel megadása kötelező.

> * Ha a [Poster](https://www.getpostman.com/) -t használja, a következő lesz a **AuthURL** : `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` érvényes, de `https://api.timeseries.azure.com` nem.

### <a name="managed-identities"></a>Felügyelt identitások

Ha Azure App Service vagy functions szolgáltatáshoz fér hozzá, kövesse az [Azure-erőforrások jogkivonatok beszerzése](../app-service/overview-managed-identity.md)című témakör útmutatását.

> [!TIP]
> A .NET-alkalmazások és-függvények esetében a felügyelt identitással való munka legegyszerűbb módja a .NET-hez készült [Azure Identity ügyféloldali kódtár](/dotnet/api/overview/azure/identity-readme) . 

A .NET-alkalmazások és-függvények esetében a felügyelt identitással való munka legegyszerűbb módja a Microsoft. Azure. Services. AppAuthentication csomag. Ez a csomag az egyszerűség és a biztonsági előnyök miatt népszerű. A fejlesztők egyszer is írhatnak kódot, és lehetővé teszik az ügyféloldali kódtár számára az alkalmazási környezet alapján történő hitelesítést – akár fejlesztői munkaállomáson, akár fejlesztői fiók használatával, akár az Azure-ban, felügyelt szolgáltatás identitásával. Áttelepítési útmutató a megelőző AppAuthentication könyvtárában olvassa el a AppAuthentication az Azure-ba című témakört. [identitás-áttelepítési útmutató](/dotnet/api/overview/azure/app-auth-migration).

Jogkivonatot kér Azure Time Series Insights C# és a .NET-hez készült Azure Identity ügyféloldali kódtár használatával:

   ```csharp
   using Azure.Identity;
   // ...
   var credential = new DefaultAzureCredential();
   var token = credential.GetToken(
   new Azure.Core.TokenRequestContext(
       new[] { "https://api.timeseries.azure.com/" }));
   var accessToken = token.Token;
   ```

### <a name="app-registration"></a>Alkalmazásregisztráció

* A fejlesztők a [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) használatával szerezhetnek be jogkivonatokat az alkalmazás regisztrálásához.

A MSAL számos alkalmazási forgatókönyvben használható, beleértve a következőket:

* [Egyoldalas alkalmazások (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Webalkalmazás-aláírás a felhasználó nevében, és webes API meghívása a felhasználó nevében](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Webes API, amely egy másik alsóbb rétegbeli webes API-t hív meg a bejelentkezett felhasználó nevében](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [A bejelentkezett felhasználó nevében webes API-t hívó asztali alkalmazás](../active-directory/develop/scenario-desktop-overview.md)
* A [mobil alkalmazás a webes API-t hívja interaktív módon bejelentkezett felhasználó nevében](../active-directory/develop/scenario-mobile-overview.md).
* [Desktop/Service Daemon-alkalmazás a webes API-t saját nevében hívja meg](../active-directory/develop/scenario-daemon-overview.md)

A példában szereplő C#-kód azt mutatja be, hogyan lehet jogkivonat beszerzése alkalmazás-regisztrálási és-lekérdezési adatok Gen2-környezetből, tekintse meg a minta alkalmazást a [githubon](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Ha [Azure Active Directory Authentication Libraryt (ADAL)](../active-directory/azuread-dev/active-directory-authentication-libraries.md) használ, olvassa el a [MSAL-re való áttelepítéssel](../active-directory/develop/msal-net-migration.md)kapcsolatos tudnivalókat.

## <a name="common-headers-and-parameters"></a>Gyakori fejlécek és paraméterek

Ez a szakasz a gyakori HTTP-kérelmek fejléceit és a Azure Time Series Insights Gen1 és Gen2 API-kkal való lekérdezések végrehajtásához használt paramétereket ismerteti. Az API-specifikus követelmények részletesebben szerepelnek a [Azure Time Series Insights REST API](/rest/api/time-series-insights/)dokumentációjában.

> [!TIP]
> A REST API-k használatáról, a HTTP-kérelmek elvégzéséről és a HTTP-válaszok kezeléséről az [Azure REST API dokumentációjában](/rest/api/azure/) olvashat bővebben.

### <a name="http-headers"></a>HTTP-fejlécek

Az alábbiakban a szükséges kérések fejléceit mutatjuk be.

| Kötelező kérelem fejléce | Leírás |
| --- | --- |
| Engedélyezés | Azure Time Series Insights használatával történő hitelesítéshez érvényes OAuth 2,0 tulajdonosi jogkivonatot kell átadni az [engedélyezési fejlécben](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate). |

> [!TIP]
> Tekintse át az üzemeltetett Azure Time Series Insights [Client SDK minta vizualizációját](https://tsiclientsample.azurewebsites.net/) , amelyből megtudhatja, hogyan végezheti el a hitelesítést az Azure Time Series Insights API-kkal programozott módon a [JavaScript ügyféloldali SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) -val a diagramok és diagramok használatával.

Az opcionális kérések fejlécei alább olvashatók.

| Választható kérelemfejléc | Leírás |
| --- | --- |
| Content-Type | csak `application/json` a támogatott. |
| x-MS-Client-Request-ID | Egy ügyfél-kérelem azonosítója. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás nyomkövetési műveletet végez a szolgáltatások között. |
| x-MS-Client-Session-ID | Ügyfél-munkamenet-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi, hogy a szolgáltatás a kapcsolódó műveletek egy csoportját nyomon követhessék a szolgáltatások között. |
| x-MS-Client-Application-Name | Annak az alkalmazásnak a neve, amely a kérelmet generálta. A szolgáltatás rögzíti ezt az értéket. |

Nem kötelező, de az ajánlott válasz fejlécek az alábbiakban olvashatók.

| Válasz fejléce | Leírás |
| --- | --- |
| Content-Type | Csak `application/json` a támogatott. |
| x-MS-Request-ID | Kiszolgáló által generált kérelem azonosítója. Felhasználható arra, hogy felvegye a kapcsolatot a Microsofttal egy kérelem kivizsgálására. |
| x-MS-Property – nem található – viselkedés | A GA API opcionális válaszának fejléce. A lehetséges értékek a következők: `ThrowError` (alapértelmezett) vagy `UseNull` . |

### <a name="http-parameters"></a>HTTP-paraméterek

> [!TIP]
> A szükséges és választható lekérdezési információkkal kapcsolatos további információkért tekintse meg a [dokumentációt](/rest/api/time-series-insights/).

A kötelező URL-lekérdezési karakterlánc paraméterei az API-verziótól függenek.

| Kiadás | API-verziók értékei |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

A nem kötelező URL-lekérdezési karakterlánc paraméterei közé tartozik a HTTP-kérelem végrehajtási időkorlátjának beállítása.

| Választható lekérdezési paraméter | Leírás | Verzió |
| --- |  --- | --- |
| `timeout=<timeout>` | Kiszolgálóoldali időtúllépés a HTTP-kérelmek végrehajtásához. Csak a [környezeti események beolvasása](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) és a [környezeti összesítések API-k beszerzése](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) esetén alkalmazható. Az időtúllépési értéknek ISO 8601 időtartam formátumúnak kell lennie, például a tartományon belül kell lennie `"PT20S"` `1-30 s` . Az alapértelmezett érték `30 s`. | Gen1 |
| `storeType=<storeType>` | A meleg tárolást engedélyező Gen2 környezetek esetén a lekérdezés a vagy a rendszeren is végrehajtható `WarmStore` `ColdStore` . A lekérdezésben szereplő paraméter határozza meg, hogy a lekérdezés melyik tárolóban legyen végrehajtva. Ha nincs meghatározva, a rendszer végrehajtja a lekérdezést a hűtőházi tárolón. A meleg tároló lekérdezéséhez be kell állítani a **storeType** `WarmStore` . Ha nincs meghatározva, a rendszer végrehajtja a lekérdezést a hűtőházi tárolón. | Gen2 |

## <a name="next-steps"></a>Következő lépések

* A Gen1 Azure Time Series Insights API-t meghívó mintakód esetében olvassa el a [lekérdezési Gen1 a C# használatával](./time-series-insights-query-data-csharp.md)című részt.

* A Gen2 Azure Time Series Insights API-kód mintáit meghívó mintakód a C# használatával olvassa be a [lekérdezési Gen2](./time-series-insights-update-query-data-csharp.md).

* Az API-referenciákkal kapcsolatos információkért olvassa el a [lekérdezési API](/rest/api/time-series-insights/reference-query-apis) dokumentációját.
