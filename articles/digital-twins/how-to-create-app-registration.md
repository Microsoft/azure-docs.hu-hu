---
title: Alkalmazásregisztráció létrehozása
titleSuffix: Azure Digital Twins
description: 'Lásd: Azure AD-alkalmazás regisztrációjának létrehozása hitelesítési lehetőségként ügyfélalkalmazások számára.'
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: acb5457f10c54a741a738dd8a1008e703b0f23b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051021"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Alkalmazás-regisztráció létrehozása az Azure Digital Twins használatával

Az Azure Digital Twins-példányok használatakor gyakori, hogy az ügyfélalkalmazások, például egy egyéni ügyfélalkalmazás vagy egy olyan minta, mint például az [Azure Digital Twins Explorer](quickstart-adt-explorer.md)használatával kommunikálnak az adott példánnyal. Ezeknek az alkalmazásoknak hitelesíteniük kell az Azure Digital Twins szolgáltatással, hogy együttműködjön vele, és az alkalmazások által használható [hitelesítési mechanizmusok](how-to-authenticate-client.md) némelyike egy [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) **alkalmazás regisztrálását** vonja maga után.

Ez az összes hitelesítési forgatókönyv esetében nem szükséges. Ha azonban olyan hitelesítési stratégiát vagy kódrészletet használ, amely az alkalmazás regisztrálását igényli, beleértve az ügyfél- **azonosítót** és a **bérlő azonosítóját** is, ez a cikk bemutatja, hogyan állíthat be egyet.

## <a name="using-azure-ad-app-registrations"></a>Az Azure AD-alkalmazások regisztrációjának használata

[Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatása. Az alkalmazások Azure AD-beli **regisztrációjának** beállítása az egyik módja, ha az ügyfélalkalmazások hozzáférést biztosítanak az Azure digitális Twins szolgáltatáshoz.

Az alkalmazás regisztrálása az [Azure Digital Twins API](how-to-use-apis-sdks.md)-khoz való hozzáférési engedélyek konfigurálására szolgál. Később az ügyfélalkalmazások a regisztráció **ügyfelének és a bérlői azonosító értékének** használatával hitelesíthetők az alkalmazás regisztrálásával, így a konfigurált hozzáférési engedélyek is megadhatók az API-khoz.

>[!TIP]
> Érdemes lehet új alkalmazás-regisztrációt beállítani minden alkalommal, amikor csak egyszer kell megtennie, *vagy* ezt csak egyszer kell megadnia.

## <a name="create-the-registration"></a>A regisztráció létrehozása

Először navigáljon a [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) a Azure Portalban (ezt a hivatkozást használhatja, vagy megkeresheti a portálon található keresősáv használatával). Válassza a szolgáltatás menü *Alkalmazásregisztrációk* elemét, majd az *+ új regisztráció* lehetőséget.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Tekintse meg az Azure AD szolgáltatás oldalát a Azure Portal, és válassza ki a &quot;Alkalmazásregisztrációk&quot; menüpontot és a &quot;+ új regisztráció&quot; gombot.":::

A következő *alkalmazás regisztrálása* lapon adja meg a kért értékeket:
* **Név**: a regisztrációhoz TÁRSÍTANDÓ Azure ad-alkalmazás megjelenítendő neve
* **Támogatott fióktípus**: *csak az ebben a szervezeti könyvtárban lévő fiókok kijelölése (csak alapértelmezett címtár – egyetlen bérlő)*
* **Átirányítási URI**: Azure *ad-alkalmazás válaszának URL-címe* az Azure ad-alkalmazáshoz. Adjon hozzá egy *nyilvános ügyfél/natív (mobile & Desktop) URI-* t a következőhöz: `http://localhost` .

Ha elkészült, kattintson a *regisztráció* gombra.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Az &quot;alkalmazás regisztrálása&quot; oldal megtekintése a leírt értékekkel":::

A regisztráció befejezését követően a portál átirányítja a részleteket tartalmazó lapjára.

## <a name="collect-client-id-and-tenant-id"></a>Ügyfél-azonosító és bérlői azonosító gyűjtése

Ezután gyűjtsön néhány fontos értéket az alkalmazás regisztrációjának részletei lapról:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Az alkalmazás regisztrációjának fontos értékeit a portálon tekintheti meg":::

Jegyezze **fel a lapon** megjelenő _**alkalmazás (ügyfél) azonosítóját**_ és _**KÖNYVTÁRát (bérlői azonosítóját)**_ . Ezek az értékek, amelyeknek az ügyfélalkalmazás ezt a regisztrációt kell használnia az Azure digitális ikrekkel való hitelesítéshez.

## <a name="provide-azure-digital-twins-api-permission"></a>Azure digitális Twins API-engedély megadása

Ezután konfigurálja a létrehozott alkalmazás-regisztrációt az Azure Digital Twins API-k alapkonfigurációs engedélyeivel.

Az alkalmazás regisztrációjának portál lapján válassza az API- *engedélyek* lehetőséget a menüből. A következő engedélyek lapon kattintson a *+ engedély hozzáadása* gombra.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Tekintse meg az alkalmazás regisztrációját a Azure Portalban, és válassza ki az API-engedélyek menüpontot és az &quot;engedély hozzáadása&quot; gombot":::

Az alábbi *API-engedélyek kérése* lapon váltson a *saját szervezet által használt API* -k lapra, és keressen rá az *Azure Digital Twins* kifejezésre. Válassza az _**Azure Digital Twins**_ lehetőséget a keresési eredmények közül, és folytassa az engedélyek hozzárendelését az Azure Digital Twins API-khoz.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Az &quot;API-engedélyek kérése&quot; oldal keresési eredményei, amely az Azure Digital Twins-t jeleníti meg az 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 alkalmazás (ügyfél) azonosítójával.":::

>[!NOTE]
> Ha az előfizetése továbbra is rendelkezik egy meglévő Azure Digital Twins-példánnyal a szolgáltatás előző nyilvános előzetes verziójából (2020. július előtt), akkor helyette az _**Azure Smart Spaces szolgáltatást**_ kell keresnie és kiválasztania. Ez az API-k ugyanazon készletének régebbi neve (figyelje meg, hogy az *alkalmazás (ügyfél) azonosítója* megegyezik a fenti képernyőképen szereplővel), és a felhasználói élmény ezen a lépésen túl nem változik.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Az &quot;API-engedélyek kérése&quot; oldal keresési eredményének megtekintése az Azure Smart Spaces szolgáltatás megjelenítése":::

Ezután kiválaszthatja, hogy mely engedélyeket kell megadni ezekhez az API-khoz. Bontsa ki az **olvasási (1)** engedélyt, és jelölje be az *olvasás. írás* az alkalmazás regisztrációs olvasójának és az író engedélyének megadására szolgáló jelölőnégyzetet.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="A &quot;kérelmek API-engedélyei&quot; oldal megtekintése az Azure Digital Twins API-khoz tartozó &quot;Read. Write&quot; engedélyek kiválasztásával":::

Ha elkészült, kattintson a *hozzáadási engedélyek* elemre.

### <a name="verify-success"></a>Sikeres ellenőrzés

Az *API-engedélyek* lapon ellenőrizze, hogy már van-e olyan bejegyzés az Azure digitális Twins számára, amely az olvasási/írási jogosultságokat tükrözi:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Az Azure AD-alkalmazás regisztrálásához szükséges API-engedélyek portál nézete, amely az Azure Digital Twins olvasási/írási hozzáférését mutatja":::

Azt is ellenőrizheti, hogy az Azure Digital Twins-hoz kapcsolódott-e az alkalmazás regisztrációjának *manifest.jsján*, amely automatikusan frissült az Azure digitális Twins információi között az API-engedélyek hozzáadásakor.

Ehhez válassza ki a *jegyzékfájlt* a menüből az alkalmazás regisztrációs jegyzékfájljának megtekintéséhez. Görgessen a Code (kód) ablak aljára, és keresse meg a következő mezőket `requiredResourceAccess` . Az értékeknek meg kell egyezniük az alábbi képernyőképen láthatókkal:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Az Azure AD-alkalmazás regisztrálási jegyzékfájljának portál nézete. A &quot;requiredResourceAccess&quot; alá ágyazva van egy &quot;resourceAppId&quot; érték a 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 és egy &quot;resourceAccess > id&quot; értékkel 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Ha ezek az értékek hiányoznak, próbálkozzon újra az [API-engedély hozzáadására szolgáló szakaszban](#provide-azure-digital-twins-api-permission)leírt lépésekkel.

## <a name="other-possible-steps-for-your-organization"></a>A szervezet egyéb lehetséges lépései

Lehetséges, hogy a szervezete további műveleteket igényel az előfizetés tulajdonosai/rendszergazdái számára az alkalmazások regisztrálásának sikeres beállítása érdekében. A szükséges lépések az intézmény által megadott beállításoktól függően változhatnak.

Íme az előfizetés tulajdonosától/rendszergazdájától megkövetelt néhány gyakori művelet. Ezek és egyéb műveletek a Azure Portal [*Azure ad alkalmazás regisztrációk*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) oldaláról is elvégezhetők.
* Nyújtson rendszergazdai hozzájárulást az alkalmazás regisztrációjához. Előfordulhat, hogy a szervezete *rendszergazdai* jogosultságokkal rendelkezik, globálisan be van kapcsolva az Azure ad-ben az előfizetésben lévő összes alkalmazás regisztrálásához. Ha igen, a tulajdonosnak/rendszergazdának be kell jelölnie ezt a gombot a vállalat számára az alkalmazás regisztrációjának *API-engedélyei* lapján, hogy az alkalmazás regisztrációja érvényes legyen:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="A &quot;rendszergazdai jóváhagyás megadása&quot; gomb portál nézete az API-engedélyek alatt":::
  - Ha a beleegyezikés sikeresen megtörtént, az Azure Digital Twins bejegyzésében szerepelnie kell a _**(vállalata)** számára engedélyezett_ *állapot* értékének.
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="A vállalat számára az API-engedélyek alatt megadott rendszergazdai engedély portál nézete":::
* Nyilvános ügyfél-hozzáférés aktiválása
* A webes és asztali hozzáférés adott válasz URL-címeinek beállítása
* Implicit OAuth2 hitelesítési folyamatok engedélyezése

Az alkalmazások regisztrálásával és a különböző telepítési lehetőségekkel kapcsolatos további információkért lásd: [*alkalmazás regisztrálása a Microsoft Identity platformon*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Azure AD-alkalmazás regisztrációját állítja be, amely az ügyfélalkalmazások Azure digitális Twins API-kkal való hitelesítésére használható.

Ezután olvassa el a hitelesítési mechanizmusokkal kapcsolatos tudnivalókat, például az alkalmazások regisztrációját használó alkalmazásokat, valamint azokat, amelyek nem:
* [*Útmutató: az alkalmazás-hitelesítési kód írása*](how-to-authenticate-client.md)