---
title: Alkalmazásregisztráció létrehozása
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan hozhat létre Azure AD-alkalmazásregisztrációt az ügyfélalkalmazások hitelesítési lehetőségeként.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd45bb264f8e29a2aad870a7daff45fdd44e0d3c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478811"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Alkalmazásregisztráció létrehozása a Azure Digital Twins

Amikor egy Azure Digital Twins példánysal dolgozik, gyakori, hogy ügyfélalkalmazásokkal, például egyéni ügyfélalkalmazással vagy egy mintával , például a [Azure Digital Twins Explorer.](quickstart-azure-digital-twins-explorer.md) Ezeknek az alkalmazásoknak hitelesítést kell végeznie a Azure Digital Twins-val [](how-to-authenticate-client.md) a használatukhoz, és az alkalmazások által használható hitelesítési mechanizmusok némelyike egy Azure Active Directory [(Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) **alkalmazásregisztrációt igényel.**

Ez nem szükséges minden hitelesítési forgatókönyvhöz. Ha azonban olyan hitelesítési stratégiát vagy kódmintát használ, amely nem igényel alkalmazásregisztrációt, beleértve **az** ügyfél-azonosítót és a bérlőazonosítót, ez a cikk bemutatja, hogyan állíthat be egyet.

## <a name="using-azure-ad-app-registrations"></a>Azure AD-alkalmazásregisztrációk használata

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatása. Az **Alkalmazásregisztráció beállítása az** Azure AD-ban az egyik módja annak, hogy hozzáférést biztosítsunk az ügyfélalkalmazásnak a Azure Digital Twins.

Ebben az alkalmazásregisztrációban konfigurálhatja a hozzáférési engedélyeket a Azure Digital Twins [API-khoz.](how-to-use-apis-sdks.md) Később az ügyfélalkalmazások hitelesítést is végezni tudnak az alkalmazásregisztrációban a regisztráció ügyfél- és bérlőazonosítójának értékeivel, ennek eredményeképp pedig meg kell adni a konfigurált hozzáférési engedélyeket az API-khoz.

>[!TIP]
> Előfordulhat, hogy minden alkalommal új alkalmazásregisztrációt szeretne *beállítani,* amikor csak szüksége van rá, vagy ha csak egyszer szeretne ilyen regisztrációt létesítni, akkor egyetlen alkalmazásregisztrációt hoz létre, amely az összes olyan forgatókönyv között meg lesz osztva, amely ezt igényli.

## <a name="create-the-registration"></a>A regisztráció létrehozása

Először navigálhat a [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a Azure Portal (ezt a hivatkozást használhatja, vagy megkeresheti a portál keresősávjában). Válassza *Alkalmazásregisztrációk* elemet a szolgáltatásmenüből, majd *az + Új regisztráció lehetőséget.*

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Az Azure AD szolgáltatásoldalának nézete a Azure Portal, kiemelve a &quot;Alkalmazásregisztrációk&quot; menüpontot és az &quot;+ Új regisztráció&quot; gombot":::

A következő *Alkalmazás regisztrálása* lapon adja meg a kért értékeket:
* **Név:** A regisztrációhoz társítható Azure AD-alkalmazás megjelenítendő neve
* **Támogatott fióktípusok:** Csak az ebben a szervezeti címtárban található fiókok *(csak alapértelmezett címtár – Egybérlős)*
* **Átirányítási URI:** *Az Azure AD-alkalmazás válasz-URL-címe* az Azure AD-alkalmazáshoz. Adjon hozzá *egy nyilvános ügyfél/natív (mobil & asztali)* URI-t a `http://localhost` számára.

Ha elkészült, kattintson a Regisztráció *gombra.*

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Az &quot;Alkalmazás regisztrálása&quot; oldal nézete a megadott értékekkel":::

Ha a regisztráció befejeződött, a portál átirányítja a részletek oldalára.

## <a name="collect-client-id-and-tenant-id"></a>Ügyfél-azonosító és bérlőazonosító gyűjtése

Ezután gyűjtsön össze néhány fontos értéket az alkalmazásregisztrációról a részletek oldalán:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Az alkalmazásregisztráció fontos értékeinek portálnézete":::

Jegyezze fel az oldalon megjelenő alkalmazás- _**(ügyfél-)**_ azonosítót és címtár- _**(bérlő-)**_ azonosítót.  Az ügyfélalkalmazásnak ezekre az értékekre lesz szüksége a regisztrációhoz a Azure Digital Twins.

## <a name="provide-azure-digital-twins-api-permission"></a>Api Azure Digital Twins engedélyek megszabadozása

Ezután konfigurálja a létrehozott alkalmazásregisztrációt alapengedélyekkel a Azure Digital Twins API-khoz.

Az alkalmazásregisztráció portáloldalán válassza az *API-engedélyek* lehetőséget a menüből. A következő engedélyek lapon kattintson a *+ Engedély hozzáadása gombra.*

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Az alkalmazásregisztráció megtekintése a Azure Portal, kiemelve az &quot;API-engedélyek&quot; menüpontot és az &quot;+ Engedély hozzáadása&quot; gombot":::

A következő *API-engedélyek* kérése lapon váltson a szervezet által használt API-k lapra, és keressen rá az *azure digital twins kifejezésre.*  Válassza _**Azure Digital Twins**_ lehetőséget a keresési eredmények közül, és rendelje hozzá az engedélyeket a Azure Digital Twins API-khoz.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Az &quot;API-engedélyek kérése&quot; oldal keresési eredményének nézete, amely az Azure Digital Twins-t mutatja, a 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 alkalmazásazonosítóval.":::

>[!NOTE]
> Ha az előfizetés még mindig rendelkezik egy meglévő Azure Digital Twins-példánysal a szolgáltatás előző nyilvános előzetes kiadásában (2020 júliusa előtt), akkor ehelyett az _**Azure Smart Spaces**_ szolgáltatást kell keresnie és kiválasztania. Ez ugyanannak az API-készletnek egy régebbi neve (figyelje meg, hogy az alkalmazás *(ügyfél)* azonosítója ugyanaz, mint a fenti képernyőképen látható), és a felhasználói élmény nem változik meg ezen a lépésen túl.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Az &quot;API-engedélyek kérése&quot; oldal keresési eredményének nézete, amely az Azure Smart Spaces szolgáltatást mutatja":::

Ezután ki kell választania, hogy mely engedélyeket adja meg ezekhez az API-khoz. **Bontsa ki az Olvasás (1)** engedélyt, és jelölje be a *Read.Write* jelölőnégyzetet az alkalmazásregisztráció olvasói és írói engedélyeinek megadásához.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Az API-engedélyek kérése oldal nézete, amely a &quot;Read.Write&quot; engedélyeket kiválasztja a Azure Digital Twins API-khoz":::

Ha *elkészült, nyomja* le az Engedélyek hozzáadása gombra.

### <a name="verify-success"></a>Sikeresség ellenőrzése

Az *API-engedélyek lapon* ellenőrizze, hogy van-e olyan bejegyzés a Azure Digital Twins olvasási/írási engedélyekkel:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Az Azure AD-alkalmazásregisztráció API-engedélyeinek portálnézete, amely az &quot;Olvasási/írási hozzáférés&quot; adatokat mutatja Azure Digital Twins":::

Azt is ellenőrizheti, hogy a kapcsolat Azure Digital Twins-e az alkalmazásregisztráció *manifest.js-ban,* amely automatikusan frissült a Azure Digital Twins-információkkal az API-engedélyek hozzáadásakor.

Ehhez válassza a *menü Jegyzékfájl* parancsát az alkalmazásregisztráció jegyzékkódja megtekintéséhez. Görgessen a kódablak aljára, és keresse meg ezeket a mezőket a `requiredResourceAccess` alatt. Az értékeknek egyezniük kell az alábbi képernyőképen látható értékekkel:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Az Azure AD-alkalmazásregisztráció jegyzékfájljának portálnézete. A &quot;requiredResourceAccess&quot; alatt van beágyazva, A resourceAppId értéke 0b07f429-9f4b-4714-9392-cc5e8e80c8b0, a resourceAccess > id értéke pedig 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Ha hiányoznak ezek az értékek, próbálja meg újra a(z) szakaszban található lépéseket az [API-engedély hozzáadásához.](#provide-azure-digital-twins-api-permission)

## <a name="other-possible-steps-for-your-organization"></a>A szervezet egyéb lehetséges lépései

Lehetséges, hogy a szervezet további műveleteket igényel az előfizetés tulajdonosaitól/rendszergazdáitól az alkalmazásregisztráció sikeres beállításához. A szükséges lépések az intézmény által megadott beállításoktól függően változhatnak.

Íme az előfizetés tulajdonosától/rendszergazdájától megkövetelt néhány gyakori művelet. Ezeket és más műveleteket a Azure AD alkalmazás [*regisztrációk*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) oldalán Azure Portal.
* Nyújtson rendszergazdai hozzájárulást az alkalmazás regisztrációjához. Előfordulhat, hogy  a szervezetnél globálisan bekapcsolt rendszergazdai jóváhagyás szükséges az Azure AD-ban az előfizetésen belüli összes alkalmazásregisztrációhoz. Ha igen, a tulajdonosnak/rendszergazdának ezt a gombot kell  választania a cég számára az alkalmazásregisztráció API-engedélyeinek oldalán, hogy az alkalmazásregisztráció érvényes legyen:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Az API-engedélyek alatti &quot;Rendszergazdai jóváhagyás megadása&quot; gomb portálnézete":::
  - Ha a jóváhagyást sikeresen megadták, a Azure Digital Twins bejegyzésének a Megadott állapotértéket kell mutatnia a következő _**számára: (a vállalata)**_ 
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="A vállalat API-engedélyekkel megadott rendszergazdai hozzájárulásának portálnézete":::
* Nyilvános ügyfél-hozzáférés aktiválása
* Konkrét válasz URL-címek beállítása webes és asztali hozzáféréshez
* Implicit OAuth2 hitelesítési folyamatok engedélyezése

További információ az alkalmazásregisztrációról és annak különböző beállítási lehetőségeiről: [*Alkalmazás regisztrálása a Microsoft identitásplatformján.*](/graph/auth-register-app-v2)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben beállít egy Azure AD-alkalmazásregisztrációt, amely az ügyfélalkalmazások hitelesítésére használható a Azure Digital Twins API-okkal.

Ezután olvassa el a hitelesítési mechanizmusokat, köztük azt, amely alkalmazásregisztrációkat használ, és másokat, amelyek nem:
* [*How-to( Alkalmazáshitelesítési kód írása)*](how-to-authenticate-client.md)