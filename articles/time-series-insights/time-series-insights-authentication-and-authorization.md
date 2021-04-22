---
title: API-hitelesítés és -engedélyezés – Azure Time Series Insights | Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhatja a hitelesítést és az engedélyezést egy olyan egyéni alkalmazáshoz, amely a Azure Time Series Insights API-t.
ms.service: time-series-insights
author: deepakpalled
ms.author: shresha
manager: dpalled
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/23/2021
ms.custom: seodec18, has-adal-ref, devx-track-azurecli
ms.openlocfilehash: 8e50b650eaffe3d0ec8d3d2cd1841bd139d33750
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867511"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Hitelesítés és engedélyezés az Azure Time Series Insights API-hoz

Az üzleti igényektől függően a megoldás egy vagy több ügyfélalkalmazást is tartalmazhat, amelyek használatával kommunikálhat a Azure Time Series Insights [környezetÉNEK API-ival.](/rest/api/time-series-insights/reference-data-access-overview) Azure Time Series Insights Azure AD biztonsági jogkivonatokkal végez hitelesítést [az OAUTH 2.0 alapján.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Az ügyfél(ök) hitelesítéséhez be kell szereznie egy megfelelő engedélyekkel rendelkező jogkivonatot, és át kell adnia az API-hívásokkal együtt. Ez a dokumentum számos módszert ismertet a hitelesítő adatok lekért felhasználásával, amelyek segítségével be lehet szerezni a jogkivonatokat és hitelesíteni lehet őket, beleértve a felügyelt identitás és az Azure Active Directory alkalmazásregisztráció használatát.

## <a name="managed-identities"></a>Felügyelt identitások

A következő szakaszok azt ismertetik, hogyan használhatja a felügyelt identitást Azure Active Directory (Azure AD) a Azure Time Series Insights API eléréséhez. Az Azure-ban a felügyelt identitások szükségtelenné teszi, hogy a fejlesztőknek kezelniük kell a hitelesítő adatokat azáltal, hogy identitást szolgáltatásokat stb. kínálnak az Azure AD-beli Azure-erőforráshoz, és ezzel szereznek be Azure Active Directory -jogkivonatokat. A felügyelt identitások használatának néhány előnye:

- Nem kell kezelnie a hitelesítő adatokat. A hitelesítő adatok még csak nem is érhetők el.
- A felügyelt identitások használatával bármely Olyan Azure-szolgáltatásban hitelesíthet, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault.
- A felügyelt identitások további költségek nélkül használhatók.

A felügyelt identitások két típusának további információiért olvassa el a Mi [az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

A felügyelt identitásokat a következőből használhatja:

- Azure-beli virtuális gépek
- Azure App Services
- Azure Functions
- Azure Container Instances
- és még sok más...

A [teljes listát az Azure-erőforrások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources) felügyelt identitását támogató Azure-szolgáltatások listájában láthatja.

## <a name="azure-active-directory-app-registration"></a>Azure Active Directory alkalmazásregisztráció

Ha lehetséges, javasoljuk a felügyelt identitások használatát, hogy ne legyen szükség a hitelesítő adatok kezelésére. Ha az ügyfélalkalmazást nem a felügyelt identitásokat támogató Azure-szolgáltatás üzemelteti, regisztrálhatja az alkalmazást egy Azure AD-bérlővel. Amikor regisztrálja az alkalmazást az Azure AD-ban, olyan identitáskonfigurációt hoz létre az alkalmazáshoz, amely lehetővé teszi az Azure AD-val való integrációt. Amikor regisztrál egy alkalmazást [](https://portal.azure.com/)a Azure Portal-ben, kiválaszthatja, hogy az egyetlen bérlő (csak a bérlőn belül érhető el) vagy több-bérlős (más bérlőkben érhető el), és opcionálisan átirányítási URI-t is beállíthat (ahová a hozzáférési jogkivonatot küldi).

Az alkalmazásregisztráció befejezése után az alkalmazásnak (az alkalmazásobjektumnak) egy globálisan egyedi példánya lesz, amely az otthoni bérlőben vagy címtárban található. Emellett rendelkezik egy globálisan egyedi azonosítóval is az alkalmazáshoz (az alkalmazás vagy az ügyfél-azonosító). A portálon ezután titkos kulcsok, tanúsítványok és hatókörök felvételére is lehetőség van az alkalmazás megfelelő munkához, az alkalmazás arculatának testreszabásához a bejelentkezési párbeszédpanelen, és így tovább.

Ha regisztrál egy alkalmazást a portálon, a rendszer automatikusan létrehoz egy alkalmazásobjektumot és egy szolgáltatásnév-objektumot a saját bérlőben. Ha a Microsoft Graph API-k használatával regisztrál/hoz létre egy alkalmazást, a szolgáltatásnév-objektum létrehozása külön lépés. A jogkivonatok lekéréséhez szolgáltatásnév-objektumra van szükség.

Mindenképpen tekintse át az alkalmazás [biztonsági](../active-directory/develop/identity-platform-integration-checklist.md#security) ellenőrzőlistát. Ajánlott eljárásként a tanúsítvány hitelesítő adatait [használja,](../active-directory/develop/active-directory-certificate-credentials.md)ne a jelszóhoz (titkos ügyfélhez) használt hitelesítő adatokat.

További [részletekért lásd](../active-directory/develop/app-objects-and-service-principals.md) a Azure Active Directory és szolgáltatásnév-objektumokat.

## <a name="step-1-create-your-managed-identity-or-app-registration"></a>1. lépés: A felügyelt identitás vagy az alkalmazásregisztráció létrehozása

Miután azonosította, hogy felügyelt identitást vagy alkalmazásregisztrációt fog használni, a következő lépés a kiépítés.

### <a name="managed-identity"></a>Felügyelt identitás

A felügyelt identitás létrehozásához használt lépések attól függően változnak, hogy hol található a kód, és hogy rendszer által hozzárendelt vagy felhasználó által hozzárendelt identitást hoz-e létre. A [különbségért olvassa](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) el a felügyelt identitások típusait. Miután kiválasztotta az identitástípust, keresse meg és kövesse a megfelelő oktatóanyagot az Azure AD által felügyelt identitások [dokumentációjában.](../active-directory/managed-identities-azure-resources/index.yml) Itt találja a felügyelt identitások konfigurálási útmutatóját a következő hez:

- [Azure-beli virtuális gépek](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-during-creation-of-a-vm)
- [App Service és Azure Functions](../app-service/overview-managed-identity.md)
- [Azure Container Instances](../container-instances/container-instances-managed-identity.md)
- és még sok más...

### <a name="application-registration"></a>Alkalmazásregisztráció

Kövesse az Alkalmazás regisztrálása [listában szereplő lépéseket.](../active-directory/develop/quickstart-register-app.md#register-an-application)

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="step-2-grant-access"></a>2. lépés: Hozzáférés megadása

Amikor a Azure Time Series Insights környezet kérést kap, a rendszer először ellenőrzi a hívó bearer jogkivonatát. Ha az érvényesítés megfelel, a hívó hitelesítése meg történik, majd egy újabb ellenőrzéssel meg kell győződni arról, hogy a hívó jogosult a kért művelet elvégzésére. Bármely felhasználó vagy szolgáltatásnév jogosultságának megadásához először az Olvasó vagy a Közreműködő szerepkör hozzárendelésével kell hozzáférést engedélyeznie számára a környezethez.

- Ha a felhasználói felületen [Azure Portal](https://portal.azure.com/) hozzáférést, kövesse az [Adatok hozzáférésének](concepts-access-policies.md) megadása környezethez cikkben felsorolt utasításokat. A felhasználó kiválasztásakor megkeresheti a felügyelt identitást vagy az alkalmazásregisztrációt a neve vagy azonosítója alapján.

- Ha az Azure CLI-t használva hozzáférést ad, futtassa a következő parancsot. A hozzáférés [kezeléséhez](/cli/azure/tsi/access-policy) rendelkezésre álló parancsok teljes listáját az itt található dokumentációban találhatja meg.

   ```azurecli-interactive
   az tsi access-policy create --name "ap1" --environment-name "env1" --description "some description" --principal-object-id "aGuid" --roles Reader Contributor --resource-group "rg1"
   ```

> [!Note]
> Az Azure CLI timeseriesinsights bővítményéhez 2.11.0-s vagy újabb verzió szükséges. A bővítmény automatikusan telepíti az az tsi access-policy parancs első futtatásakor. [További információ a](/cli/azure/azure-cli-extensions-overview) bővítményekről.

## <a name="step-3-requesting-tokens"></a>3. lépés: Jogkivonatok kérése

Miután kiépítte és hozzárendelte a felügyelt identitást vagy az alkalmazásregisztrációt egy szerepkörhöz, megkezdheti a használatukat az OAuth 2.0-beli bearer tokenek igényléséhez. A jogkivonat beszerzésének módja a kód helyétől és a választott nyelvtől függően eltérő lehet. Az erőforrás (más néven a jogkivonat "célközönsége") megadásakor a jogkivonat URL Azure Time Series Insights GUID-ja alapján azonosítható:

* `https://api.timeseries.azure.com/`
* `120d688d-1518-4cf7-bd38-182f158850b6`

> [!IMPORTANT]
> Ha az URL-címet használja erőforrás-azonosítóként, a jogkivonatot pontosan a következőnek kell kiállítania: `https://api.timeseries.azure.com/` . A záró perjel kötelező.

> * A [Postman használata esetén](https://www.getpostman.com/) az **AuthURL** tehát a következő lesz: `https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?scope=https://api.timeseries.azure.com//.default`
> * `https://api.timeseries.azure.com/` érvényes, de `https://api.timeseries.azure.com` nem.

### <a name="managed-identities"></a>Felügyelt identitások

Ha az azure Azure App Service vagy a Functionsből fér hozzá, kövesse az [Azure-erőforrások jogkivonatának beszerzése dokumentum útmutatását.](../app-service/overview-managed-identity.md)

A .NET-alkalmazások és -függvények esetében a felügyelt identitások használatának legegyszerűbb módja az [Azure Identity](/dotnet/api/overview/azure/identity-readme) .NET-hez való ügyféloldali kódtára. Ez az ügyféloldali kódtár az egyszerűség és a biztonsági előnyök miatt népszerű. A fejlesztők megírhatják egyszer a kódot, és az ügyfélkódtárra hagyhatják, hogy az alkalmazáskörnyezet alapján határozzák meg a hitelesítést – legyen szó akár fejlesztői munkaállomásról, akár egy fejlesztői fiókról, akár felügyeltszolgáltatás-identitással üzembe helyezett Azure-ban. Az előd appAuthentication kódtár migrálási útmutatójához olvassa el az [AppAuthentication to Azure.Identity Migration Guidance (AppAuthentication – Azure.Identity áttelepítési útmutató) útmutatót.](/dotnet/api/overview/azure/app-auth-migration)

Jogkivonat kérése Azure Time Series Insights C# és a .NET-hez való Azure Identity ügyféloldali kódtár használatával:

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

* A fejlesztők a [Microsoft Authentication Library](../active-directory/develop/msal-overview.md) (MSAL) használatával jogkivonatokat szerezhetnek be az alkalmazásregisztrációkhoz.

Az MSAL számos alkalmazási helyzetben használható, többek között a következőkben:

* [Egyoldalas alkalmazások (JavaScript)](../active-directory/develop/scenario-spa-overview.md)
* [Webalkalmazás, amely bejelentkezik egy felhasználóba, és egy webes API-t hív meg a felhasználó nevében](../active-directory/develop/scenario-web-app-call-api-overview.md)
* [Egy másik lefelé irányuló webes API-t hívó webes API a bejelentkezett felhasználó nevében](../active-directory/develop/scenario-web-api-call-api-overview.md)
* [Webes API-t a bejelentkezett felhasználó nevében hívó asztali alkalmazás](../active-directory/develop/scenario-desktop-overview.md)
* [A webes API-t](../active-directory/develop/scenario-mobile-overview.md)az interaktívan bejelentkezett felhasználó nevében hívó mobilalkalmazás.
* [A webes API-t saját nevében hívó asztali/szolgáltatás démonalkalmazás](../active-directory/develop/scenario-daemon-overview.md)

A jogkivonatok alkalmazásregisztrációként való lekért és Gen2-környezetből való lekérdezését bemutató C#-mintakódért tekintse meg a mintaalkalmazást a [GitHubon](https://github.com/Azure-Samples/Azure-Time-Series-Insights/blob/master/gen2-sample/csharp-tsi-gen2-sample/DataPlaneClientSampleApp/Program.cs)

> [!IMPORTANT]
> Ha az [MSAL Azure Active Directory tárat (ADAL) használja,](../active-directory/azuread-dev/active-directory-authentication-libraries.md) olvassa el a [következőt: MSAL.](../active-directory/develop/msal-net-migration.md)

## <a name="common-headers-and-parameters"></a>Gyakori fejlécek és paraméterek

Ez a szakasz a Gen1 és Gen2 Azure Time Series Insights API-k lekérdezéséhez használt gyakori HTTP-kérésfejléceket és -paramétereket ismerteti. Az API-specifikus követelményeket részletesebben a referenciadokumentáció [Azure Time Series Insights REST API ismerteti.](/rest/api/time-series-insights/)

> [!TIP]
> Az [Azure REST API](/rest/api/azure/) referencia-útmutatója további információt tartalmaz a REST API-k használatának, a HTTP-kérések igénylésének és a HTTP-válaszok kezelésének elsajátításról.

### <a name="http-headers"></a>HTTP-fejlécek

A szükséges kérelemfejléceket az alábbiakban ismertetjük.

| Szükséges kérelemfejléc | Description |
| --- | --- |
| Engedélyezés | A hitelesítéshez Azure Time Series Insights OAuth 2.0 bearer jogkivonatot kell áteríteni az [Authorization fejlécben.](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate) |

> [!TIP]
> Az üzemeltetett Azure Time Series Insights [SDK](https://tsiclientsample.azurewebsites.net/) mintavizualizációból megtudhatja, hogyan hitelesítheti magát programozott módon a Azure Time Series Insights API-jaival a [JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) ügyféloldali SDK-val diagramokkal és grafikonokkal együtt.

A nem kötelező kérelemfejléceket az alábbiakban ismertetjük.

| Választható kérelemfejléc | Description |
| --- | --- |
| Tartalomtípus | csak `application/json` az támogatott. |
| x-ms-client-request-id | Egy ügyfélkérés-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi a szolgáltatás számára a műveletek nyomon követését a szolgáltatások között. |
| x-ms-client-session-id | Egy ügyfél-munkamenet-azonosító. A szolgáltatás rögzíti ezt az értéket. Lehetővé teszi a szolgáltatás számára a kapcsolódó műveletek egy csoportjának nyomon követését a szolgáltatások között. |
| x-ms-client-application-name | A kérést generáló alkalmazás neve. A szolgáltatás rögzíti ezt az értéket. |

A választható, de javasolt válaszfejléceket az alábbiakban ismertetjük.

| Válaszfejléc | Description |
| --- | --- |
| Tartalomtípus | Csak `application/json` a támogatott. |
| x-ms-request-id | A kiszolgáló által létrehozott kérés azonosítója. A kérések kivizsgálására használható a Microsofttal való kapcsolatfelvételhez. |
| x-ms-property-not-found-behavior | GA API választható válaszfejléc. Lehetséges értékek: `ThrowError` (alapértelmezett) vagy `UseNull` . |

### <a name="http-parameters"></a>HTTP-paraméterek

> [!TIP]
> A kötelező és opcionális lekérdezési információkról a referenciadokumentációban [talál további információt.](/rest/api/time-series-insights/)

A szükséges URL-lekérdezési sztringparaméterek az API verziójától függnek.

| Kiadás | API-verzióértékek |
| --- |  --- |
| Gen1 | `api-version=2016-12-12`|
| Gen2 | `api-version=2020-07-31`|

Az URL-lekérdezési sztring választható paraméterei közé tartozik a HTTP-kérések végrehajtási idejekorlát beállítása.

| Nem kötelező lekérdezési paraméter | Leírás | Verzió |
| --- |  --- | --- |
| `timeout=<timeout>` | A HTTP-kérések végrehajtásának kiszolgálóoldali időkorlátja. Csak a Get [Environment Events](/rest/api/time-series-insights/dataaccess(preview)/query/getavailability) és a Get [Environment Aggregates API-kra](/rest/api/time-series-insights/gen1-query-api#get-environment-aggregates-api) vonatkozik. Az időtúllépési értéknek például ISO 8601 időtartamformátumban kell lennie, és a `"PT20S"` tartományon belül kell `1-30 s` lennie. Az alapértelmezett érték `30 s`. | Gen1 |
| `storeType=<storeType>` | A 2. generációs környezetekben, ahol engedélyezve van a meleg tároló, a lekérdezés a vagy a használatával `WarmStore` hajtható `ColdStore` végre. A lekérdezés ezen paramétere határozza meg, hogy melyik tárolón kell végrehajtani a lekérdezést. Ha nincs meghatározva, a lekérdezés a hidegen tárolt tárolón lesz végrehajtva. A warm store lekérdezéséhez a **storeType** tulajdonságot a következőre kell beállítani: `WarmStore` . Ha nincs meghatározva, a lekérdezés a hideg tárolón lesz végrehajtva. | Gen2 |

## <a name="next-steps"></a>Következő lépések

* Az 1. generációs Azure Time Series Insights API-t behívó mintakódért olvassa el az 1. generációs adatok C# használatával [való lekérdezését.](./time-series-insights-query-data-csharp.md)

* A Gen2 api-kódmintákat Azure Time Series Insights Gen2-adatokat C# használatával lekérdező [kódért olvassa el.](./time-series-insights-update-query-data-csharp.md)

* Az API-referenciainformációkért olvassa el a [Query API referenciadokumentációját.](/rest/api/time-series-insights/reference-query-apis)
