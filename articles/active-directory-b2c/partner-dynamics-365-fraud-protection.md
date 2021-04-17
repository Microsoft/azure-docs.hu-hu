---
title: 'Oktatóanyag: Azure Active Directory B2C Microsoft Dynamics 365 Fraud Protection segítségével'
titleSuffix: Azure AD B2C
description: 'Oktatóanyag: a Azure Active Directory B2C a Microsoft Dynamics 365 Fraud Protection segítségével a kockázatos és rosszindulatú fiókok azonosításához'
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8c9d760ed888eb194ad8f282f180a634e3c09538
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587816"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Oktatóanyag: A Microsoft Dynamics 365 Fraud Protection konfigurálása Azure Active Directory B2C

Ebben a minta oktatóanyagban útmutatást nyújtunk a [Microsoft Dynamics 365 Fraud Protection](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) és az Azure Active Directory (AD) B2C integrálásához.

A Microsoft DFP lehetővé teszi az ügyfelek számára annak kiértékelését, hogy az új fiókok létrehozására tett kísérletek és az ügyfél ökoszisztémába való bejelentkezési kísérletének kockázata csalárd-e. A Microsoft DFP-értékelés segítségével az ügyfél blokkolhatja vagy megtámadhatja az új hamis fiókok létrehozására vagy a meglévő fiókok feltörésére tett gyanús kísérleteket. A fiókvédelem magában foglalja a mesterséges intelligenciával védett eszköz-ujjlenyomatkezelést, a valós idejű kockázatfelméréshez szükséges API-kat, a szabályokat és a listára vonatkozó felhasználói élményt a kockázatstratégia az ügyfél üzleti igényeinek megfelelően való optimalizálásához, valamint egy mutatószámrendszert a csalás elleni védelem hatékonyságának és trendjeinek figyelése érdekében az ügyfél ökoszisztémájában.

Ebben a példában a Microsoft DFP fiókvédelmi funkcióit integráljuk egy Azure AD B2C folyamattal. A szolgáltatás minden bejelentkezési vagy bejelentkezési kísérlet után ujjlenyomattal fog ujjlenyomatot venni, és minden múltbeli vagy gyanús viselkedést figyel. Azure AD B2C egy döntési végpontot hív meg a Microsoft DFP-ről, amely az azonosított felhasználó összes múltbeli és jelenlegi viselkedése, valamint a Microsoft DFP szolgáltatásban megadott egyéni szabályok alapján ad vissza eredményt. Azure AD B2C az eredmény alapján hoz egy jóváhagyási döntést, és ugyanezt továbbítja a Microsoft DFP-nek.

## <a name="prerequisites"></a>Előfeltételek

A első lépésekhez a következőre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes fiókot [is kaphat.](https://azure.microsoft.com/free/)

- Egy [Azure AD B2C bérlő.](./tutorial-create-tenant.md) A bérlő az Azure-előfizetéséhez van kapcsolva.

- Szerezze be a Microsoft [DFP-előfizetést.](https://dynamics.microsoft.com/pricing/#Sales) A próbaverziót [is beállíthatja.](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin)

## <a name="scenario-description"></a>Forgatókönyv leírása

A Microsoft DFP-integráció a következő összetevőket tartalmazza:

- **Azure AD B2C bérlő:** Hitelesíti a felhasználót, és a Microsoft DFP ügyfeleként működik. Ujjlenyomat-parancsfájlt futtat, amely a célként megadott szabályzatot végző összes felhasználó azonosítási és diagnosztikai adatait gyűjti. A későbbiekben letiltja vagy kihívást jelent a bejelentkezési vagy bejelentkezési kísérletek, ha a Microsoft DFP gyanúsnak találja őket.

- **Egyéni app service:** Egy webalkalmazás, amely két célt szolgál.

  - A felhasználói felületéhez Identity Experience Framework HTML-oldalakat szolgál ki. A Microsoft Dynamics 365 ujjlenyomat-parancsfájl beágyazásáért felelős.

  - EGY RESTful-végpontokat használó API-vezérlő, amely összekapcsolja a Microsoft DFP-t a Azure AD B2C. Kezeli az adatfeldolgozást, a struktúrát, és mindkét biztonsági követelménynek megfelel.

- **Microsoft DFP** ujjlenyomat-készítés szolgáltatás: Dinamikusan beágyazott parancsfájl, amely az eszköz telemetriai adatait és az önkiszolgáló felhasználói adatokat naplózza egy egyedi azonosításra alkalmas ujjlenyomat létrehozásához, hogy a felhasználó később használva legyen a döntéshozatali folyamatban.

- **Microsoft DFP API-végpontok:** A döntés eredményét biztosítja, és az ügyfélalkalmazás által végzett műveletet tükröző végső állapotot fogad el. Azure AD B2C nem kommunikál közvetlenül a végpontokkal a különböző biztonsági és API-hasznos adatokra vonatkozó követelmények miatt, hanem köztesként használja az App Service-t.

Az alábbi architektúradiagram az implementációt mutatja be.

![A kép a microsoft dynamics365 csalás elleni védelem architektúráját ábrázoló diagramot ábrázolja](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalra érkezik. A felhasználók a Regisztráció lehetőséget választva létrehoznak egy új fiókot, és adatokat írnak be az oldalra. Azure AD B2C gyűjti a felhasználói attribútumokat.
| 2. | Azure AD B2C a középső réteg API-ját, és átadja a felhasználói attribútumokat.
| 3. | A középső réteg API-ja összegyűjti a felhasználói attribútumokat, és a Microsoft DFP API által használható formátumba alakítja át őket. Ezután elküldi a Microsoft DFP API-nak.
| 4. | Miután a Microsoft DFP API feldolgozza és feldolgozza az információkat, visszaadja az eredményt a középső réteg API-jának.
| 5. | A középső réteg API feldolgozza az információkat, és visszaküldi a releváns információkat a Azure AD B2C.
| 6. | Azure AD B2C a középső réteg API-tól kap információt. Ha hibaválaszt jelenít meg, hibaüzenet jelenik meg a felhasználó számára. Ha sikeres választ ad, a rendszer hitelesíti a felhasználót, és beírja a címtárba.

## <a name="set-up-the-solution"></a>A megoldás beállítása

1. [Hozzon létre egy Olyan](./identity-provider-facebook.md#create-a-facebook-application) Facebook-alkalmazást, amely engedélyezi az összevonást Azure AD B2C.
2. [Adja hozzá a létrehozott Facebook-titkos](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) kulcsot Identity Experience Framework szabályzatkulcsként.

## <a name="configure-your-application-under-microsoft-dfp"></a>Az alkalmazás konfigurálása a Microsoft DFP-ben

[Állítsa be az Azure AD-bérlőt](/dynamics365/fraud-protection/integrate-real-time-api) a Microsoft DFP használatára.

## <a name="deploy-to-the-web-application"></a>Üzembe helyezés a webalkalmazásban

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>A Microsoft DFP szolgáltatás ujjlenyomat-felismerésének megvalósítása

[A Microsoft DFP-eszközök ujjlenyomatának](/dynamics365/fraud-protection/device-fingerprinting) használata a Microsoft DFP-fiókok védelmének egyik követelménye.

>[!NOTE]
>A felhasználói Azure AD B2C oldalain kívül az ügyfél az alkalmazáskódban is implementálja az ujjlenyomat-készítés szolgáltatást az átfogóbb eszközprofil-készítés érdekében. Ez a minta nem tartalmazza az ujjlenyomat-olvasó szolgáltatást az alkalmazáskódban.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>A Azure AD B2C API-kód üzembe helyezése

A megadott [API-kód üzembe helyezése](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) egy Azure-szolgáltatásban. A kód az [Visual Studio.](/visualstudio/deployment/quickstart-deploy-to-azure)

CORS beállítása, Engedélyezett **forrás hozzáadása**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Később szüksége lesz az üzembe helyezett szolgáltatás URL-címére, hogy konfigurálni tudja az Azure AD-t a szükséges beállításokkal.

További [információért tekintse meg az App Service](../app-service/app-service-web-tutorial-rest-api.md) dokumentációját.

### <a name="add-context-dependent-configuration-settings"></a>Környezetfüggő konfigurációs beállítások hozzáadása

Konfigurálja az alkalmazásbeállításokat az [Azure App Service-beli szolgáltatásban.](../app-service/configure-common.md#configure-app-settings) Ez lehetővé teszi a beállítások biztonságos konfigurálését anélkül, hogy be kellene őket ellenőrizni egy adattárba. A Rest API-nak a következő beállításokra van szüksége:

| Alkalmazásbeállítások | Forrás | Jegyzetek |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Microsoft DFP-konfiguráció |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | A Microsoft-eszköz ujjlenyomat-azonosítójának használata |     |
| FraudProtectionSettings:ApiBaseUrl |  Az Ön alap URL-címe a Microsoft DFP portálról   | Az "-int" eltávolítása az éles API hívásához|
|  TokenProviderConfig: Erőforrás  | Az Ön alap URL-címe – https://api.dfp.dynamics-int.com     | Az "-int" eltávolítása az éles API hívásához|
|   TokenProviderConfig:ClientId       |A Fraud Protection kereskedelmi Azure AD-ügyfélalkalmazás-azonosítója      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | A Fraud Protection kereskedelmi Azure AD-bérlői hatósága |
| TokenProviderConfig:CertificateThumbprint* | A kereskedelmi Azure AD-ügyfélalkalmazással való hitelesítéshez használt tanúsítvány ujjlenyomata |
| TokenProviderConfig:ClientSecret* | A kereskedő Azure AD-ügyfélalkalmazásának titkos útjára | Ajánlott titkos kulcsok kezelőjeként használni |

*A 2 megjelölt paraméterből csak 1-et állítson be attól függően, hogy tanúsítvánnyal vagy titkos okkal, például jelszóval hitelesíti-e magát.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C konfigurálása

### <a name="replace-the-configuration-values"></a>A konfigurációs értékek cseréje

A megadott egyéni [szabályzatban](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)keresse meg a következő helyőrzőket, és cserélje le őket a példány megfelelő értékeire.

| Helyőrző | Csere erre | Jegyzetek |
| :-------- | :------------| :-----------|
|{your_tenant_name} | A bérlő rövid neve |  "yourtenant" a yourtenant.onmicrosoft.com   |
|{your_tenantId} | A Azure AD B2C bérlőazonosítója |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   A saját bérlőben konfigurált IdentityExperienceFramework alkalmazás Azure AD B2C azonosítója    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  A saját bérlőben konfigurált ProxyIdentityExperienceFramework alkalmazás Azure AD B2C azonosítója      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  A bérlő tárolóalkalmazásának alkalmazásazonosítója   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | A bérlő tárolóalkalmazásának objektumazonosítója    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Az App Insights-példány eszközkulcsa*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Végpont az App Service-ben, ahonnan a felhasználói felület fájljait szolgálja ki    | `https://yourapp.azurewebsites.net/B2CUI/GetUIPage`   |
|   {your_app_service_url}  | Az App Service URL-címe    |  `https://yourapp.azurewebsites.net`  |
|   {your-facebook-app-id}  |  Az összevonáshoz konfigurált Facebook-alkalmazás alkalmazásazonosítója Azure AD B2C   | 000000000000000   |
|  {your-facebook-app-secret}   |  Annak a szabályzatkulcsnak a neve, amelyként mentette a Facebook alkalmazáskulcsát   | B2C_1A_FacebookAppSecret   |

*Az App Insights egy másik bérlőben is lehet. Ez a lépés nem kötelező. Ha nem szükséges, távolítsa el a megfelelő TechnicalProfiles és OrechestrationSteps értékeket.

### <a name="call-microsoft-dfp-label-api"></a>A Microsoft DFP label API hívása

Az ügyfeleknek implementálja [a label API-t.](/dynamics365/fraud-protection/integrate-ap-api) További [információ: Microsoft DFP API.](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0)

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

A userID értékének meg kell egy lennie a megfelelő konfigurációs értékben (ObjectID Azure AD B2C értékben megadott értékkel.

>[!NOTE]
>Hozzájárulási értesítés hozzáadása az attribútumgyűjtemény oldalhoz. Értesítse arról, hogy a rendszer rögzíti a felhasználók telemetriai és felhasználói identitási adatait a fiók védelme érdekében.

## <a name="configure-the-azure-ad-b2c-policy"></a>Az Azure AD B2C konfigurálása

1. A Házirendek [Azure AD B2C meg a](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) szabályzatot.

2. Kövesse ezt a [dokumentumot](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack) a [LocalAccounts kezdőcsomag letöltéséhez](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Konfigurálja a szabályzatot a Azure AD B2C bérlőhöz.

>[!NOTE]
>Frissítse a megadott szabályzatokat, hogy az adott bérlőhöz kapcsolódnak.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a Szabályzatok alatt válassza a **Identity Experience Framework.**

2. Válassza ki a korábban **létrehozott SignUpSignIn jelölőt.**

3. Válassza **a Felhasználói folyamat futtatása lehetőséget,** és válassza ki a beállításokat:

   a. **Alkalmazás:** válassza ki a regisztrált alkalmazást (a minta A JWT)

   b. **Válasz URL-cím:** válassza ki az **átirányítási URL-címet**

   c. Válassza **a Felhasználói folyamat futtatása lehetőséget.**

4. A regisztráció folyamatának végigmenve hozzon létre egy fiókot

5. A felhasználói attribútum létrehozása után a rendszer a folyamat során a Microsoft DFP szolgáltatást fogja meghívni. Ha a folyamat nem teljes, ellenőrizze, hogy a felhasználó nincs-e mentve a könyvtárban.

>[!NOTE]
>Ha Microsoft DFP-szabálymotort használ, frissítse a szabályokat közvetlenül a [Microsoft DFP portálon.](/dynamics365/fraud-protection/rules)

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- [Microsoft DFP-minták](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Az egyéni szabályzatok első lépések a Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
