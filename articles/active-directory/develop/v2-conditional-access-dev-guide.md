---
title: Fejlesztői útmutató Azure Active Directory feltételes hozzáféréshez
titleSuffix: Microsoft identity platform
description: Fejlesztői útmutató és forgatókönyvek az Azure AD feltételes hozzáféréshez és a Microsoft Identity platformhoz.
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 05/18/2020
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.openlocfilehash: ed8007c81479c73e4503d74af4c4043e503baf2b
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120146"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Fejlesztői útmutató Azure Active Directory feltételes hozzáféréshez

Azure Active Directory (Azure AD) feltételes hozzáférési funkciója számos módszert kínál az alkalmazás biztonságossá tételéhez és a szolgáltatások védelméhez. A feltételes hozzáférés lehetővé teszi a fejlesztők és a nagyvállalatok számára, hogy számos módon védik a szolgáltatásokat, többek között:

* [Többtényezős hitelesítés](../authentication/concept-mfa-howitworks.md)
* Csak az Intune-ban regisztrált eszközök hozzáférésének engedélyezése adott szolgáltatásokhoz
* Felhasználói helyszínek és IP-címtartományok korlátozása

A feltételes hozzáférés teljes képességeivel kapcsolatos további információkért tekintse meg a [feltételes hozzáférésről](../conditional-access/overview.md)szóló cikket.

Az Azure AD-alkalmazások fejlesztésére szolgáló fejlesztők számára ez a cikk bemutatja, hogyan használhatja a feltételes hozzáférést, és megtudhatja, hogy milyen hatással van az olyan erőforrásokhoz való hozzáférésre, amelyeken nem szabályozható a feltételes hozzáférési szabályzatok alkalmazása. A cikk azt is vizsgálja, hogy milyen hatással van a feltételes hozzáférés a folyamat, a webalkalmazások, a Microsoft Graph elérése és az API-k hívása során.

A rendszer feltételezi az [önálló](quickstart-register-app.md) és a [több-bérlős](howto-convert-app-to-be-multi-tenant.md) alkalmazások, valamint a [gyakori hitelesítési minták](./authentication-vs-authorization.md) ismeretét.

> [!NOTE]
> A szolgáltatás használatához prémium szintű Azure AD P1 licenc szükséges. A követelményeinek leginkább megfelelő licenc kiválasztásáról lásd [az ingyenes, alapszintű és prémium kiadások általánosan elérhető szolgáltatásait összehasonlító cikket](https://azure.microsoft.com/pricing/details/active-directory/).
> A [Microsoft 365 vállalati verzió licenccel](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek hozzáférhetnek a feltételes hozzáférési funkciókhoz is.

## <a name="how-does-conditional-access-impact-an-app"></a>Hogyan befolyásolja a feltételes hozzáférés az alkalmazást?

### <a name="app-types-impacted"></a>Érintett alkalmazások típusai

A leggyakoribb esetek többségében a feltételes hozzáférés nem változtatja meg az alkalmazás viselkedését, vagy a fejlesztőtől semmilyen változást nem igényel. Csak bizonyos esetekben, amikor egy alkalmazás közvetve vagy csendes módon kér le egy jogkivonatot a szolgáltatáshoz, az alkalmazásnak a feltételes hozzáférési kihívások kezelése érdekében programkód-módosításokat kell megadnia. Lehet, hogy olyan egyszerű, mint egy interaktív bejelentkezési kérés végrehajtása.

Az alábbi forgatókönyvek a feltételes hozzáférési kihívások kezelésére vonatkozó kódot igényelnek:

* A folyamaton kívüli folyamatot végrehajtó alkalmazások
* Több szolgáltatáshoz/erőforráshoz hozzáférő alkalmazások
* Egylapos alkalmazások MSAL.js használatával
* Erőforrás meghívása Web Apps

A feltételes hozzáférési szabályzatok alkalmazhatók az alkalmazásra, de az alkalmazáshoz hozzáférő webes API-ra is alkalmazhatók. A feltételes hozzáférési szabályzat konfigurálásával kapcsolatos további tudnivalókért tekintse meg a következőt: gyors útmutató: többtényezős hitelesítés [megkövetelése adott alkalmazásokhoz Azure Active Directory feltételes hozzáféréssel](../authentication/tutorial-enable-azure-mfa.md).

A forgatókönyvtől függően a nagyvállalati ügyfelek bármikor alkalmazhatják és eltávolíthatják a feltételes hozzáférési szabályzatokat. Ahhoz, hogy az alkalmazás továbbra is működőképes legyen egy új házirend alkalmazása esetén, alkalmazza a kihívások kezelését. Az alábbi példák a kihívások kezelését szemléltetik.

### <a name="conditional-access-examples"></a>Feltételes hozzáférési példák

Egyes esetekben a feltételes hozzáférés kezelésére kód módosítására van szükség, míg mások ugyanúgy működnek. Íme néhány forgatókönyv a feltételes hozzáférés használatával a többtényezős hitelesítéshez, amely némi betekintést nyújt a különbségbe.

* Létrehoz egy egybérlős iOS-alkalmazást, és alkalmazza a feltételes hozzáférési szabályzatot. Az alkalmazás aláírja a felhasználót, és nem kér hozzáférést egy API-hoz. Amikor a felhasználó bejelentkezik, a rendszer automatikusan meghívja a szabályzatot, és a felhasználónak többtényezős hitelesítést (MFA) kell végrehajtania.
* Olyan natív alkalmazást hoz létre, amely egy középső rétegbeli szolgáltatást használ egy alsóbb rétegbeli API eléréséhez. Az alkalmazást használó vállalat vállalati ügyfele az alsóbb rétegbeli API-ra alkalmazza a szabályzatot. Amikor a végfelhasználó bejelentkezik, a natív alkalmazás hozzáférést kér a középső szinten, és elküldi a jogkivonatot. A középső réteg az alsóbb rétegbeli API-hoz való hozzáférés kéréséhez a folyamaton kívüli folyamatot hajt végre. Ezen a ponton a középső szinten a "Challenge" jogcím jelenik meg. A középső szinten visszaküldi a kihívást a natív alkalmazásnak, amelynek meg kell felelnie a feltételes hozzáférési szabályzatnak.

#### <a name="microsoft-graph"></a>Microsoft Graph

A Microsoft Graph speciális szempontokat is figyelembe kell vennie az alkalmazások feltételes hozzáférési környezetekben való létrehozásakor. Általánosságban a feltételes hozzáférés mechanikája ugyanúgy viselkedik, de a felhasználók által megjelenített szabályzatok az alkalmazás által a gráf által kért mögöttes adatokon alapulnak.

Pontosabban, az összes Microsoft Graph hatókör olyan adatkészletet jelöl, amely külön házirendeket alkalmaz. Mivel a feltételes hozzáférési szabályzatok az adott adatkészletekhez vannak rendelve, az Azure AD a Graph mögötti adat alapján kényszeríti a feltételes hozzáférési szabályzatokat, a gráf helyett.

Ha például egy alkalmazás a következő Microsoft Graph hatóköröket kéri le:

```
scopes="ChannelMessages.Read.All Mail.Read"
```

Egy alkalmazás arra számíthat, hogy a felhasználók teljesítik a csapatok és az Exchange szolgáltatásban beállított összes házirendet. Bizonyos hatókörök több adatkészletre is leképezhetők, ha hozzáférést biztosítanak.

### <a name="complying-with-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzatnak való megfelelés

Számos különböző alkalmazás-topológiához a rendszer kiértékel egy feltételes hozzáférési szabályzatot a munkamenet létrehozásakor. Mivel a feltételes hozzáférési szabályzat az alkalmazások és szolgáltatások részletességén működik, a meghívott pont nagy mértékben függ a végrehajtani kívánt forgatókönyvtől.

Amikor az alkalmazás egy feltételes hozzáférési szabályzattal próbál hozzáférni egy szolgáltatáshoz, előfordulhat, hogy feltételes hozzáférési kihívással találkozik. Ez a kihívás az `claims` Azure ad válaszában kapott paraméterben van kódolva. Íme egy példa erre a Challenge paraméterre:

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A fejlesztők igénybe vehetik ezt a kihívást, és hozzáfűzik azt egy új Azure AD-kéréshez. Ha átadja ezt az állapotot, a rendszer felszólítja a végfelhasználót, hogy végezzen el minden olyan műveletet, amely a feltételes hozzáférési szabályzat betartásához szükséges. A következő esetekben a rendszer a hiba részleteit és a paraméter kibontását ismerteti.

## <a name="scenarios"></a>Forgatókönyvek

### <a name="prerequisites"></a>Előfeltételek

Az Azure AD feltételes hozzáférés a [prémium szintű Azure ad](../fundamentals/active-directory-whatis.md)részét képező szolgáltatás. A [Microsoft 365 vállalati verzió licenccel](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) rendelkező ügyfelek hozzáférhetnek a feltételes hozzáférési funkciókhoz is.

### <a name="considerations-for-specific-scenarios"></a>Bizonyos forgatókönyvek szempontjai

A következő információk csak a feltételes hozzáférési forgatókönyvekben érvényesek:

* A folyamaton kívüli folyamatot végrehajtó alkalmazások
* Több szolgáltatáshoz/erőforráshoz hozzáférő alkalmazások
* Egylapos alkalmazások MSAL.js használatával

Az alábbi fejezetek az összetettebb általános forgatókönyveket tárgyalják. Az alapszintű működési elv a feltételes hozzáférési házirendek kiértékelése, amikor a rendszer a jogkivonatot arra a szolgáltatásra kéri, amelyhez feltételes hozzáférési szabályzat van alkalmazva.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Forgatókönyv: a folyamaton kívüli folyamatot végrehajtó alkalmazás

Ebben az esetben a natív alkalmazás egy webszolgáltatást/API-t hív meg. Ez a szolgáltatás pedig a "folyamatban lévő" folyamatot hívja fel egy alsóbb rétegbeli szolgáltatás meghívására. A mi esetünkben a feltételes hozzáférési szabályzatot alkalmazták az alsóbb rétegbeli szolgáltatásra (webes API 2), és nem kiszolgálói/démoni alkalmazás helyett natív alkalmazást használnak.

![A folyamaton belüli adatdiagramot végrehajtó alkalmazás](./media/v2-conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

A webes API 1-es verziójának kezdeti jogkivonat-kérelme nem kéri a végfelhasználót a többtényezős hitelesítéshez, mert a webes API 1 nem mindig éri el az alsóbb rétegbeli API-t. Amint a webes API 1 megpróbál a felhasználó nevében a (z) 2. webes API-ra vonatkozó jogkivonatot kérni, a kérés meghiúsul, mivel a felhasználó nem jelentkezett be a többtényezős hitelesítéssel.

Az Azure AD egy HTTP-választ ad vissza néhány érdekes adattal:

> [!NOTE]
> Ebben az esetben ez egy multi-Factor Authentication-hiba leírása, de a `interaction_required` feltételes hozzáférés széles köre lehetséges.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

A webes API 1-es verziójában elkapjuk a hibát `error=interaction_required` , és visszaküldjük a `claims` kihívást az asztali alkalmazásnak. Ezen a ponton az asztali alkalmazás új hívást hajthat végre, `acquireToken()` és hozzáfűzheti a `claims` kihívást extra lekérdezési karakterlánc paraméterként. Ehhez az új kérelemhez a felhasználónak többtényezős hitelesítést kell végeznie, majd újra el kell küldenie az új jogkivonatot a webes API 1-nek, és el kell végeznie a folyamaton kívüli folyamatot.

A forgatókönyv kipróbálásához tekintse meg a [.net-kód mintáját](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph#handling-required-interactions-with-the-user-dynamic-consent-mfa-etc-). Azt mutatja be, hogyan lehet a jogcímeket visszaadni a webes API 1-ből a natív alkalmazásba, és új kérelmet létrehozni az ügyfélalkalmazás számára.

## <a name="scenario-app-accessing-multiple-services"></a>Forgatókönyv: az alkalmazás több szolgáltatáshoz fér hozzá

Ebben az esetben a webalkalmazás két szolgáltatáshoz fér hozzá, amelyek közül az egyikhez tartozik egy feltételes hozzáférési szabályzat. Az alkalmazás logikája alapján előfordulhat, hogy az alkalmazás elérési útja nem igényli mindkét webszolgáltatáshoz való hozzáférést. Ebben az esetben a jogkivonatok igénylésének sorrendje fontos szerepet játszik a végfelhasználói élményben.

Tegyük fel, hogy az A és B webszolgáltatás és A B webszolgáltatás a feltételes hozzáférési szabályzattal van alkalmazva. Noha a kezdeti interaktív hitelesítési kérelem mindkét szolgáltatáshoz beleegyezik, a feltételes hozzáférési szabályzat minden esetben nem szükséges. Ha az alkalmazás jogkivonatot kér a B webszolgáltatáshoz, akkor a rendszer meghívja a szabályzatot, és A webszolgáltatásra vonatkozó további kérések is az alábbiak szerint lesznek sikeresek.

![Az alkalmazás több szolgáltatáshoz fér hozzá.](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Ha az alkalmazás kezdetben jogkivonatot kér a webszolgáltatáshoz, a végfelhasználó nem hívja meg a feltételes hozzáférési házirendet. Ez lehetővé teszi az alkalmazás fejlesztője számára a végfelhasználói élmény szabályozását, és nem kényszeríti a feltételes hozzáférési szabályzat meghívását minden esetben. A trükkös eset az, ha az alkalmazás ezt követően jogkivonatot kér a B webszolgáltatás számára. Ezen a ponton a felhasználónak meg kell felelnie a feltételes hozzáférési szabályzatnak. Amikor az alkalmazás megpróbálja a `acquireToken` -t, a következő hibaüzenetet hozhatja elő (az alábbi ábrán látható):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Alkalmazás, amely új jogkivonatot kér több szolgáltatáshoz](./media/v2-conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Ha az alkalmazás a MSAL könyvtárat használja, a jogkivonat beszerzésének sikertelensége mindig interaktív módon próbálkozik újra. Ha ez az interaktív kérelem bekövetkezik, a végfelhasználónak lehetősége van a feltételes hozzáférés betartására. Ez csak akkor igaz, ha a kérelem olyan, `AcquireTokenSilentAsync` vagy `PromptBehavior.Never` ebben az esetben, amikor az alkalmazásnak interaktív kérelmet kell elvégeznie ```AcquireToken``` ahhoz, hogy a végfelhasználó a szabályzatnak való megfelelést lehetővé tegye.

## <a name="scenario-single-page-app-spa-using-msaljs"></a>Forgatókönyv: egyoldalas alkalmazás (SPA) MSAL.js használatával

Ebben a forgatókönyvben bemutatjuk az esetet, amikor egy egyoldalas alkalmazást (SPA) használunk MSAL.js használatával a feltételes hozzáférésű védett webes API meghívásához. Ez egy egyszerű architektúra, de van néhány olyan árnyalata, amelyet figyelembe kell venni a feltételes hozzáféréshez való fejlesztés során.

MSAL.jsban van néhány olyan függvény, amely tokeneket szerez be: `loginPopup()` , `acquireTokenSilent(...)` , `acquireTokenPopup(…)` és `acquireTokenRedirect(…)` .

* `loginPopup()` egy interaktív bejelentkezési kéréssel szerzi be az azonosító jogkivonatot, de nem kap hozzáférési jogkivonatokat bármely szolgáltatáshoz (beleértve a feltételes hozzáférésű védett webes API-t).
* `acquireTokenSilent(…)` Ezután egy hozzáférési jogkivonat csendes beszerzésére használható, ami azt jelenti, hogy nem jeleníti meg a felhasználói felületet semmilyen körülmények között.
* `acquireTokenPopup(…)` a és `acquireTokenRedirect(…)` mindkét esetben az erőforrás-tokenek interaktív igénylésére szolgálnak, ami azt jelenti, hogy mindig bejelentkezési felhasználói felületet mutatnak.

Ha egy alkalmazásnak hozzáférési tokenre van szüksége a webes API meghívásához, az megkísérli a-t `acquireTokenSilent(…)` . Ha a jogkivonat-munkamenet lejárt, vagy meg kell felelnie egy feltételes hozzáférési szabályzatnak, a *acquireToken* függvény meghiúsul, és az alkalmazás a vagy a alkalmazást használja `acquireTokenPopup()` `acquireTokenRedirect()` .

![Egyoldalas alkalmazás MSAL flow diagram használatával](./media/v2-conditional-access-dev-guide/spa-using-msal-scenario.png)

Lássunk egy példát a feltételes hozzáférési forgatókönyvre. A végfelhasználó csak a helyen landolt, és nem rendelkezik munkamenettel. `loginPopup()`Az azonosító jogkivonat beszerzése a multi-Factor Authentication használata nélkül végezhető. Ezután a felhasználó egy olyan gombot talál, amely megköveteli, hogy az alkalmazás egy webes API-ból kérjen adatkérést. Az alkalmazás megkísérli a hívást, `acquireTokenSilent()` de sikertelen, mert a felhasználó még nem hajtotta végre a többtényezős hitelesítést, és meg kell felelnie a feltételes hozzáférési szabályzatnak.

Az Azure AD a következő HTTP-választ küldi vissza:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Az alkalmazásnak meg kell fognia a következőt: `error=interaction_required` . Az alkalmazás ezt követően `acquireTokenPopup()` vagy `acquireTokenRedirect()` ugyanazon az erőforráson is használható. A felhasználónak egy többtényezős hitelesítést kell végeznie. Miután a felhasználó befejezte a többtényezős hitelesítést, az alkalmazás egy friss hozzáférési jogkivonatot bocsát ki a kért erőforráshoz.

Ha szeretné kipróbálni ezt a forgatókönyvet, tekintse [meg a JS Spa-t a kód nevében](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/a2b257381b410c765ee01ecb611aa6f98c099eb1/2.%20Web%20API%20now%20calls%20Microsoft%20Graph/README.md). Ez a mintakód azt a feltételes hozzáférési házirendet és webes API-t használja, amelyet korábban regisztrált a JS SPA használatával a forgatókönyv bemutatásához. Bemutatja, hogyan kezelheti megfelelően a jogcímek kihívását, és hogyan szerezhet be egy olyan hozzáférési jogkivonatot, amelyet a webes API-hoz használhat. Alternatív megoldásként az általános [Angular.js kódot](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) is kiválaszthatja egy szögletes fürdőre vonatkozó útmutatásért

## <a name="see-also"></a>Lásd még

* A képességekkel kapcsolatos további tudnivalókért tekintse meg a [feltételes hozzáférés Azure Active Directoryban](../conditional-access/overview.md)című témakört.
* További Azure AD-programkódok: [minták](sample-v2-code.md).
* További információ a MSAL SDK-ról és a dokumentációhoz való hozzáférésről: a [Microsoft hitelesítési függvénytárának áttekintése](msal-overview.md).
* További információ a több-bérlős forgatókönyvekről: [Bejelentkezés a felhasználókba a több-bérlős minta használatával](howto-convert-app-to-be-multi-tenant.md).
* További információ a [feltételes hozzáférésről és a IoT-alkalmazásokhoz való hozzáférés biztonságossá tételéről](/azure/architecture/example-scenario/iot-aad/iot-aad).
