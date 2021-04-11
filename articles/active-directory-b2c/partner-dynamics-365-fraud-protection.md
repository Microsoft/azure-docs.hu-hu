---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Microsoft Dynamics 365 csalások elleni védelemmel
titleSuffix: Azure AD B2C
description: Oktatóanyag a Azure Active Directory B2C konfigurálásához a Microsoft Dynamics 365 csalások elleni védelemmel a kockázatos és csalárd fiókok azonosításához
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9483b0a8829c042a7cf8d516c6007cbbf14a97ac
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639755"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Oktatóanyag: a Microsoft Dynamics 365 csalások elleni védelem konfigurálása Azure Active Directory B2C

Ebben a példában útmutatást adunk a [Microsoft Dynamics 365 csalások elleni védelem](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) a Azure Active Directory (ad) B2C-vel való integrálásához.

A Microsoft DFP lehetővé teszi az ügyfeleknek, hogy felmérjék, hogy az új fiókok létrehozására és az ügyfél ökoszisztémába való bejelentkezésre tett kísérletek kockázata csalárd-e. A Microsoft DFP Assessment segítségével az ügyfél blokkolhatja vagy megkérdőjelezheti a gyanús kísérleteket új hamis fiókok létrehozásához vagy a meglévő fiókok megtámadásához. A fiókok védelme magában foglalja a mesterséges intelligencia által felhatalmazott eszközök ujjlenyomatát, API-kat a valós idejű kockázatértékelés, a szabályok és a lista számára a kockázati stratégia optimalizálásához az ügyfél üzleti igényei szerint, valamint egy scorecardot, amely a csalások elleni védelem hatékonyságát és az ügyfél ökoszisztémájában felmerülő trendeket figyeli.

Ebben a példában a Microsoft DFP fiók-védelmi funkcióit egy Azure AD B2C felhasználói folyamattal integráljuk. A szolgáltatás külsőleg ujjlenyomatot küld minden bejelentkezéskor, vagy ha a múltbeli vagy a gyanús viselkedést is figyeli. Azure AD B2C meghívja a Microsoft DFP döntési végpontját, amely az azonosított felhasználó összes múltbeli és aktuális viselkedésén alapuló eredményt ad vissza, valamint a Microsoft DFP szolgáltatásban megadott egyéni szabályokat is. Azure AD B2C az eredmény alapján jóváhagyja a jóváhagyást, és ugyanezt a Microsoft DFP-nek adja vissza.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Azure ad B2C bérlő](./tutorial-create-tenant.md). A bérlő az Azure-előfizetéshez van csatolva.

- Microsoft DFP- [előfizetés](https://dynamics.microsoft.com/pricing/#Sales)beszerzése. Egy [próbaverziós ügyfélszoftvert](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) is beállíthat.

## <a name="scenario-description"></a>Forgatókönyv leírása

A Microsoft DFP-integráció a következő összetevőket tartalmazza:

- **Azure ad B2C bérlő**: hitelesíti a felhasználót, és a Microsoft DFP-ügyfélként működik. Az ujjlenyomat-kezelési parancsfájlt tartalmaz, amely minden olyan felhasználó azonosítási és diagnosztikai adatait gyűjti, amely egy célként megadott házirendet hajt végre. A későbbiekben blokkolja vagy észleli a bejelentkezést vagy a regisztrálási kísérleteket, ha a Microsoft DFP gyanúsnak találja őket.

- **Egyéni app Service**: egy webalkalmazás, amely két célt szolgál.

  - Az identitási élmény keretrendszere felhasználói felületén használható HTML-lapokat szolgál ki. A Microsoft Dynamics 365 ujjlenyomat-előkészítési parancsfájljának beágyazásához felelős.

  - Egy olyan REST-végpontokkal rendelkező API-vezérlő, amely a Microsoft DFP-t csatlakoztatja Azure AD B2Choz. Kezeli az adatfeldolgozást, a struktúrát és a mindkettő biztonsági követelményeit.

- **Microsoft DFP ujjlenyomat-kezelési szolgáltatás**: dinamikusan beágyazott parancsfájl, amely naplózza az eszköz telemetria és az önérvényesített felhasználói adatokat, hogy egyedi módon azonosítható ujjlenyomatot hozzon létre a felhasználó számára a döntéshozatali folyamat későbbi részében való használatra.

- **Microsoft DFP API-végpontok**: megadja a döntés eredményét, és elfogad egy végső állapotot, amely az ügyfélalkalmazás által végrehajtott műveletet tükrözi. A Azure AD B2C nem kommunikál a végpontokkal közvetlenül a különböző biztonsági és API-adattartalom-követelmények miatt, hanem közbensőként használja az App Service-t.

A következő architektúra-diagram a megvalósítást mutatja be.

![A kép a Microsoft dynamics365 csalások elleni védelmi architektúra diagramját mutatja](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Lépés | Leírás |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználók a regisztráció gombra kattintva létrehozhatnak egy új fiókot, és beírhatják az adatokat az oldalra. A Azure AD B2C felhasználói attribútumokat gyűjt.
| 2. | Azure AD B2C meghívja a középső rétegbeli API-t, és átadja a felhasználói attribútumokat.
| 3. | A középső rétegbeli API gyűjti a felhasználói attribútumokat, és átalakítja a Microsoft DFP API által felhasznált formátumba. Ezt követően küldje el a Microsoft DFP API-nak.
| 4. | Miután a Microsoft DFP API felhasználta az adatokat, és feldolgozza azt, visszaadja az eredményt a középső rétegbeli API-nak.
| 5. | A középső rétegbeli API feldolgozza az adatokat, és visszaküldi a kapcsolódó információkat a Azure AD B2Cba.
| 6. | Azure AD B2C adatokat kap vissza a középső rétegbeli API-ból. Ha hibát jelez, hibaüzenet jelenik meg a felhasználó számára. Ha sikeres választ mutat, a rendszer hitelesíti a felhasználót, és beírja a könyvtárba.

## <a name="set-up-the-solution"></a>A megoldás beállítása

1. [Hozzon létre egy Facebook-alkalmazást](./identity-provider-facebook.md#create-a-facebook-application) , amely lehetővé teszi, hogy az összevonás Azure ad B2C.
2. [Adja hozzá a létrehozott Facebook-titkot](./custom-policy-get-started.md#create-the-facebook-key) identitási élmény keretrendszerének kulcsaként.

## <a name="configure-your-application-under-microsoft-dfp"></a>Az alkalmazás konfigurálása a Microsoft DFP-ban

[Állítsa be az Azure ad-bérlőt](/dynamics365/fraud-protection/integrate-real-time-api) a Microsoft DFP használatára.

## <a name="deploy-to-the-web-application"></a>Üzembe helyezés a webalkalmazásban

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>A Microsoft DFP szolgáltatás ujjlenyomatának implementálása

A Microsoft [DFP-eszközök ujjlenyomata](/dynamics365/fraud-protection/device-fingerprinting) a Microsoft DFP-fiókok védelmének követelménye.

>[!NOTE]
>Azure AD B2C felhasználói felület oldalain kívül az ügyfél is megvalósíthatja az ujjlenyomat-kezelő szolgáltatást az alkalmazás kódjában az átfogóbb eszközök profilkészítése érdekében. Ez a minta nem tartalmaz ujjlenyomat-szolgáltatást az alkalmazás kódjában.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>A Azure AD B2C API-kód üzembe helyezése

A [megadott API-kód](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) üzembe helyezése egy Azure-szolgáltatásban. A kód [közzétehető a Visual studióból](/visualstudio/deployment/quickstart-deploy-to-azure).

CORS beállítása, **engedélyezett forrás** hozzáadása `https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Később szüksége lesz a telepített szolgáltatás URL-címére az Azure AD konfigurálásához a szükséges beállításokkal.

További információért lásd az [app Service dokumentációját](../app-service/app-service-web-tutorial-rest-api.md) .

### <a name="add-context-dependent-configuration-settings"></a>Környezettől függő konfigurációs beállítások hozzáadása

Konfigurálja az alkalmazás beállításait az [Azure app Service-](../app-service/configure-common.md#configure-app-settings)ben. Ez lehetővé teszi a beállítások biztonságos konfigurálását anélkül, hogy azokat egy adattárba kellene bejelentkeznie. A REST API-nak a következő beállításokat kell megadnia:

| Alkalmazásbeállítások | Forrás | Jegyzetek |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Microsoft DFP-konfiguráció |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Microsoft-eszköz ujjlenyomat-ügyfél-azonosítója |     |
| FraudProtectionSettings:ApiBaseUrl |  A Microsoft DFP portál alap URL-címe   | A "-int" eltávolítása a termelési API meghívásához|
|  TokenProviderConfig: erőforrás  | Az alap URL-cím – https://api.dfp.dynamics-int.com     | A "-int" eltávolítása a termelési API meghívásához|
|   TokenProviderConfig: ClientId       |A csalás elleni védelem kereskedelmi Azure AD ügyfélalkalmazás-azonosítója      |       |
| TokenProviderConfig: szolgáltató | https://login.microsoftonline.com/<directory_ID> | A csalások elleni védelem az Azure AD bérlői szolgáltatója |
| TokenProviderConfig: CertificateThumbprint * | A kereskedelmi Azure AD-ügyfélalkalmazás hitelesítéséhez használt tanúsítvány ujjlenyomata |
| TokenProviderConfig:ClientSecret* | A Merchant Azure AD-ügyfélalkalmazás titka | A Secrets Manager használata ajánlott |

* Csak a 2 megjelölt paraméterek 1 értékének beállítása attól függően, hogy a tanúsítványt vagy titkos kulcsot, például jelszót kell-e hitelesíteni.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C konfiguráció

### <a name="replace-the-configuration-values"></a>A konfigurációs értékek cseréje

A megadott [Egyéni szabályzatokban](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)keresse meg a következő helyőrzőket, és cserélje le azokat a példány megfelelő értékeire.

| Helyőrző | Csere erre | Jegyzetek |
| :-------- | :------------| :-----------|
|{your_tenant_name} | A bérlő rövid neve |  "yourtenant" a yourtenant.onmicrosoft.com   |
|{your_tenantId} | A Azure AD B2C bérlő bérlői azonosítója |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Az Azure AD B2C-bérlőben konfigurált IdentityExperienceFramework-alkalmazás alkalmazás-azonosítója    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Az Azure AD B2C-bérlőben konfigurált ProxyIdentityExperienceFramework-alkalmazás alkalmazás-azonosítója      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  A bérlő tárolási alkalmazásának alkalmazás-azonosítója   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | A bérlő Storage-alkalmazásának objektum-azonosítója    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Az alkalmazás-bepillantási példány kialakítási kulcsa *  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Végpont az App Service-ben a felhasználói felületi fájlok kiszolgálása    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | Az App Service URL-címe    |  https://yourapp.azurewebsites.net  |
|   {a-Facebook-App-ID}  |  Az Azure AD B2C-vel való összevonáshoz konfigurált Facebook-alkalmazás alkalmazás-azonosítója   | 000000000000000   |
|  {a-Facebook-App-Secret}   |  A Facebook alkalmazás titkos kódjának mentett kulcs neve   | B2C_1A_FacebookAppSecret   |

* Az alkalmazás-felismerések egy másik bérlőn is lehetnek. Ez a lépés nem kötelező. Ha nincs rá szükség, távolítsa el a megfelelő TechnicalProfiles és OrechestrationSteps.

### <a name="call-microsoft-dfp-label-api"></a>A Microsoft DFP Label API meghívása

Az ügyfeleknek meg kell [valósítaniuk a Label API](/dynamics365/fraud-protection/integrate-ap-api)-t. További információt a [Microsoft DFP API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) -val foglalkozó témakörben talál.

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

A userID értékének meg kell egyeznie a megfelelő Azure AD B2C konfigurációs értékben (ObjectID).

>[!NOTE]
>Adja hozzá a beleegyező értesítést az attribútum-gyűjtemény laphoz. Értesítés arról, hogy a felhasználók telemetria és a felhasználói identitásra vonatkozó adatokat a rendszer a fiók védelme érdekében rögzíti.

## <a name="configure-the-azure-ad-b2c-policy"></a>A Azure AD B2C házirend konfigurálása

1. Lépjen a házirendek mappában található [Azure ad B2C házirendre](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) .

2. A [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) letöltéséhez kövesse ezt a [dokumentumot](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)

3. Konfigurálja a Azure AD B2C bérlő házirendjét.

>[!NOTE]
>Frissítse a megadott bérlőhöz kapcsolódó házirendeket.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza az **identitási élmény keretrendszere** elemet.

2. Válassza ki a korábban létrehozott **SignUpSignIn**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás**: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. **Válasz URL-címe**: válassza ki az **átirányítási URL-címet**

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. A rendszer a folyamat során hívja meg a Microsoft DFP szolgáltatást, miután létrehozta a felhasználói attribútumot. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

>[!NOTE]
>Szabályok frissítése közvetlenül a Microsoft DFP-portálon a [Microsoft DFP-szabály motorjának](/dynamics365/fraud-protection/rules)használata esetén.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Microsoft DFP-minták](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
