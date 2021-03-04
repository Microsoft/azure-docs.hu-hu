---
title: Jelszó nélküli bejelentkezés Azure Active Directory
description: További tudnivalók a jelszó nélküli bejelentkezés lehetőségeiről Azure Active Directory FIDO2 biztonsági kulcsok vagy a Microsoft Authenticator alkalmazás használatával
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97517ad3ff260806108dc22686d49b43b0fc6609
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096020"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>A Azure Active Directory jelszóval nem rendelkező hitelesítési beállításai

Az olyan funkciók, mint a többtényezős hitelesítés (MFA), nagyszerű megoldást jelentenek a szervezet biztonságossá tételére, de a felhasználók gyakran csalódottak lesznek a további biztonsági réteggel, hogy emlékezzenek a jelszavára. A jelszóval nem rendelkező hitelesítési módszerek sokkal kényelmesebbek, mert a jelszó el lett távolítva, és lecserélve valamire, vagy valamire, amit tud.

| Hitelesítés  | Valami, ami az Öné | Amit Ön vagy ismer |
| --- | --- | --- |
| Jelszó nélküli | Windows 10-es eszköz, telefon vagy biztonsági kulcs | Biometrikus vagy PIN-kód |

A hitelesítéshez minden szervezetnek eltérő igényeire van szüksége. A Microsoft a következő három, a Azure Active Directory (Azure AD) szolgáltatással integrálható, jelszóval nem kompatibilis hitelesítési lehetőséget nyújt:

- Vállalati Windows Hello
- A Microsoft Authenticator alkalmazás
- FIDO2 biztonsági kulcsok

![Hitelesítés: biztonság és kényelem](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Vállalati Windows Hello

A vállalati Windows Hello ideális olyan információkkal dolgozó szakemberek számára, akik saját kijelölt Windows-számítógéppel rendelkeznek. A biometrikus és PIN-kód hitelesítő adatai közvetlenül a felhasználó SZÁMÍTÓGÉPéhez vannak kötve, ami megakadályozza a hozzáférést a tulajdonostól eltérő személyek számára. A nyilvános kulcsokra épülő infrastruktúrával (PKI) való integrációval és az egyszeri bejelentkezés (SSO) beépített támogatásával a Windows Hello for Business kényelmes módszert kínál a vállalati erőforrások zökkenőmentes elérésére a helyszínen és a felhőben.

![Felhasználói bejelentkezés a vállalati Windows Hello-ben – példa](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

A következő lépések bemutatják, hogyan működik a bejelentkezési folyamat az Azure AD-vel:

![A vállalati Windows Hello szolgáltatásban a felhasználói bejelentkezéshez szükséges lépéseket ismertető ábra](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. A felhasználó biometrikus vagy PIN-kód típusú kézmozdulattal jelentkezik be a Windowsba. A kézmozdulat feloldja a vállalati Windows Hello-kulcs zárolását, és elküldjük a Cloud Authentication biztonsági támogatási szolgáltatónak, amelyet a *Felhőbeli AP-szolgáltatónak* nevezünk.
1. A Cloud AP Provider egy egyszer használatos (véletlenszerű, tetszőleges számú) Azure AD-t kér.
1. Az Azure AD egy egypéldányos értéket ad vissza, amely 5 percig érvényes.
1. A Felhőbeli AP-szolgáltató aláírja az időpontot a felhasználó titkos kulcsa alapján, és visszaadja az aláírt alkalmi lehetőséget az Azure AD-nek.
1. Az Azure AD a felhasználó biztonságos regisztrált nyilvános kulcsával érvényesíti az aláírt alkalmi használatot az egyszeres aláírással. Az aláírás ellenőrzése után az Azure AD ezt követően érvényesíti a visszaadott, aláírt időpontot. Az egyszeres hitelesítés ellenőrzése után az Azure AD létrehoz egy elsődleges frissítési tokent (PRT) az eszköz átviteli kulcsához titkosított munkamenetkulcs használatával, és visszaadja a Felhőbeli AP-szolgáltatónak.
1. A Felhőbeli AP-szolgáltató fogadja a titkosított PRT-t a munkamenet-kulccsal. Az eszköz privát átviteli kulcsa segítségével a Cloud AP Provider visszafejti a munkamenetkulcsot, és a platformmegbízhatósági modul (TPM) használatával védi a munkamenetkulcsot.
1. A Felhőbeli AP-szolgáltató sikeres hitelesítési választ ad vissza a Windowsnak. A felhasználó ezután hozzáférhet a Windowshoz, valamint a Felhőbeli és a helyszíni alkalmazásokhoz anélkül, hogy újra kellene hitelesítenie magát (SSO).

A vállalati Windows Hello [tervezési útmutató](/windows/security/identity-protection/hello-for-business/hello-planning-guide) segítségével döntéseket hozhat a vállalati Windows Hello üzembe helyezésének típusáról és a szükséges lehetőségekről.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator alkalmazás

Azt is lehetővé teheti, hogy az alkalmazott telefonja jelszavas hitelesítési módszerként is váljon. Előfordulhat, hogy a jelszó mellett már használja a Microsoft Authenticator alkalmazást kényelmes multi-Factor Authentication beállításként. A hitelesítő alkalmazást jelszóval nem rendelkező megoldásként is használhatja.

![Bejelentkezés a Microsoft Edge-be a Microsoft Authenticator alkalmazással](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

A hitelesítő alkalmazás minden iOS-vagy Android-telefont erős, jelszóval nem rendelkező hitelesítő adatba kapcsol. A felhasználók bármilyen platformra vagy böngészőbe bejelentkezhetnek, ha értesítést küldenek a telefonjára, és a képernyőn megjelenő számot a telefonján megjelenő telefonszámra, majd a biometrikus (érintés vagy arc) vagy a PIN-kód használatával megerősítik. A telepítés részleteiért tekintse meg [a Microsoft Authenticator alkalmazás letöltése és telepítése](../user-help/user-help-auth-app-download-install.md) című témakört.

A hitelesítő alkalmazással való jelszóval nem rendelkező hitelesítés a vállalati Windows Hello szolgáltatással megegyező alapszintű mintát követi. Ez egy kicsit bonyolultabb, mivel a felhasználó azonosítására van szükség, hogy az Azure AD megtalálja a használt Microsoft Authenticator alkalmazás verzióját:

![A Microsoft Authenticator alkalmazással való felhasználói bejelentkezéshez szükséges lépéseket ismertető ábra](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. A felhasználó beírja a felhasználónevét.
1. Az Azure AD észleli, hogy a felhasználó erős hitelesítő adatokkal rendelkezik, és elindítja a hitelesítő adatok erős folyamatát.
1. Értesítés küldése az alkalmazásnak az iOS-eszközökön Apple Push Notification Service (APNS), vagy az Android-eszközökön futó Firebase Cloud Messaging (FCM) használatával.
1. A felhasználó megkapja a leküldéses értesítést, és megnyitja az alkalmazást.
1. Az alkalmazás meghívja az Azure AD-t, és befogadja a jelenléti igazolást.
1. A felhasználó a titkos kulcs feloldásához a biometrikus vagy PIN-kód megadásával befejezi a kihívást.
1. A rendszer aláírja a titkos kulcsot, és visszaküldi az Azure AD-nek.
1. Az Azure AD nyilvános/titkos kulcs érvényesítését hajtja végre, és visszaadja a tokent.

A jelszó nélküli bejelentkezéshez a következő útmutatók végrehajtásával kezdheti meg:

> [!div class="nextstepaction"]
> [Jelszó nélküli aláírás engedélyezése a hitelesítő alkalmazás használatával](howto-authentication-passwordless-phone.md)

## <a name="fido2-security-keys"></a>FIDO2 biztonsági kulcsok

A (gyors identitás online) Szövetség segíti a nyílt hitelesítési szabványok előléptetését, és csökkenti a jelszavak használatát hitelesítés formájában. A FIDO2 a legújabb szabvány, amely magában foglalja a webes hitelesítési (WebAuthn) szabványt.

A FIDO2 biztonsági kulcsai egy nem adattípusra épülő, szabványos jelszavas hitelesítési módszer, amely bármilyen típusú tényezőt tartalmazhat. A gyors identitású online (pont) egy nyílt szabvány a jelszó nélküli hitelesítéshez. A parancs lehetővé teszi, hogy a felhasználók és a szervezetek a standard használatával bejelentkezzenek az erőforrásaik számára Felhasználónév vagy jelszó nélkül, egy külső biztonsági kulccsal vagy egy eszközbe épített platform-kulccsal.

A felhasználók regisztrálhatnak és kijelölhetnek egy FIDO2 biztonsági kulcsot a bejelentkezési felületen a hitelesítés fő eszközeként. Ezek a FIDO2 biztonsági kulcsok általában USB-eszközök, de a Bluetooth vagy az NFC használatát is használhatják. Ha a hitelesítést kezelő hardvereszközt használ, a rendszer megnöveli a fiók biztonságát, mivel nincs olyan jelszó, amely elérhetővé vagy kitalált.

A FIDO2 biztonsági kulcsainak használatával bejelentkezhet az Azure AD-be vagy hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközökre, és egyszeri bejelentkezéssel bejelentkezhet a felhőbe és a helyszíni erőforrásaiba. A felhasználók a támogatott böngészőkbe is bejelentkezhetnek. A FIDO2 biztonsági kulcsai nagyszerű lehetőséget biztosítanak olyan nagyvállalatok számára, akik nagyon érzékenyek a biztonságra, vagy olyan forgatókönyvekkel vagy alkalmazottakkal rendelkeznek, akik nem hajlandók vagy nem tudják használni a telefont második tényezőként.

Rendelkezünk egy dokumentációval, amely [támogatja a FIDO2-hitelesítést az Azure ad-vel](fido2-compatibility.md), valamint ajánlott eljárásokat azon fejlesztők számára, akik támogatni szeretnék [a FIDO2-hitelesítés használatát az általuk fejlesztett alkalmazásokban](../develop/support-fido2-authentication.md).

![Bejelentkezés a Microsoft Edge-be egy biztonsági kulccsal](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

A következő folyamat akkor használatos, amikor a felhasználó egy FIDO2 biztonsági kulccsal jelentkezik be:

![A FIDO2 biztonsági kulccsal való felhasználói bejelentkezéshez szükséges lépéseket ismertető ábra](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. A felhasználó csatlakoztatja a FIDO2 biztonsági kulcsot a számítógépéhez.
2. A Windows észleli a FIDO2 biztonsági kulcsot.
3. A Windows hitelesítési kérelmet küld.
4. Az Azure AD egy alkalomból álló üzenetet küld vissza.
5. A felhasználó befejezi a kézmozdulatot a FIDO2 biztonsági kulcs biztonságos enklávéban tárolt titkos kulcs feloldásához.
6. A FIDO2 biztonsági kulcs aláírja az egyszeres kulcsot a titkos kulccsal.
7. Az Azure AD-nek az elsődleges frissítési jogkivonat (PRT) jogkivonat-kérelmét aláírt egyszer kell elküldeni.
8. Az Azure AD a FIDO2 nyilvános kulcsával ellenőrzi a bejelentkezett alkalmi használatot.
9. Az Azure AD a PRT-t a helyszíni erőforrásokhoz való hozzáférés engedélyezéséhez adja vissza.

Noha a FIDO2 által hitelesített kulcsok sok kulcsot használnak, a Microsoft a FIDO2 ügyfél és a hitelesítő protokoll (CTAP) specifikációjának bizonyos opcionális kiterjesztését igényli a gyártó által megvalósított maximális biztonság és a legjobb felhasználói élmény biztosítása érdekében.

A biztonsági kulcsnak a következő szolgáltatásokat és bővítményeket **kell** megvalósítania a FIDO2 CTAP-protokollból, hogy a Microsoft-kompatibilis legyen:

| # | Szolgáltatás/bővítmény megbízhatósága | Miért szükséges a funkció vagy a bővítmény? |
| --- | --- | --- |
| 1 | Rezidens kulcs | Ez a funkció lehetővé teszi, hogy a biztonsági kulcs hordozható legyen, ahol a hitelesítő adatokat a biztonsági kulcs tárolja. |
| 2 | Ügyfél PIN-kódja | Ez a funkció lehetővé teszi, hogy a hitelesítő adatait egy második tényezővel megvédje, és azokra a biztonsági kulcsokra vonatkozzon, amelyek nem rendelkeznek felhasználói felülettel. |
| 3 | HMAC – titkos kód | Ez a bővítmény biztosítja, hogy bejelentkezzen az eszközre, amikor az offline vagy a repülőgép üzemmódban van. |
| 4 | Több fiók/RP | Ez a funkció biztosítja, hogy ugyanazt a biztonsági kulcsot használja több szolgáltatáshoz, például a Microsoft-fiókhoz és a Azure Active Directory. |

### <a name="fido2-security-key-providers"></a>FIDO2 biztonsági kulcs szolgáltatói

A következő szolgáltatók olyan FIDO2 biztonsági kulcsokat kínálnak, amelyekről ismert, hogy kompatibilisek a jelszóval nem rendelkező felülettel. Javasoljuk, hogy a kulcsok biztonsági tulajdonságainak kiértékeléséhez vegye fel a kapcsolatot a gyártóval, valamint a következőt:.

| Szolgáltató | Kapcsolattartó |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://ftsafe.us/pages/microsoft](https://ftsafe.us/pages/microsoft) |
| HID fájlok | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| TrustKey-megoldások | [https://www.trustkeysolutions.com/security-keys/](https://www.trustkeysolutions.com/security-keys/) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Thales csoport) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/products/fido](https://www.onespan.com/products/fido) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) |
| Hypersecu | [https://www.hypersecu.com/hyperfido](https://www.hypersecu.com/hyperfido) |
| VinCSS | [https://passwordless.vincss.net](https://passwordless.vincss.net) |
| KONA I | [https://konai.com/business/security/fido](https://konai.com/business/security/fido) |
| Excelsecu | [https://www.excelsecu.com/productdetail/esecufido2secu.html](https://www.excelsecu.com/productdetail/esecufido2secu.html) |
| Token2 Svájc | [https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key](https://www.token2.swiss/shop/product/token2-t2f2-alu-fido2-u2f-and-totp-security-key) |

> [!NOTE]
> Ha NFC-alapú biztonsági kulcsok megvásárlását és megtervezését tervezi, akkor a biztonsági kulcshoz támogatott NFC-olvasóra van szükség. Az NFC-olvasó nem Azure-követelmény vagy-korlátozás. A támogatott NFC-olvasók listáját az NFC-alapú biztonsági kulcs gyártójánál találja.

Ha Ön szállító, és az eszközt a támogatott eszközök listáján szeretné beszerezni, forduljon a következőhöz: [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com) .

A FIDO2 biztonsági kulcsainak megkezdéséhez kövesse az alábbi útmutatókat:

> [!div class="nextstepaction"]
> [Jelszó nélküli aláírás engedélyezése a FIDO2 biztonsági kulcsaival](howto-authentication-passwordless-security-key.md)

## <a name="supported-scenarios"></a>Támogatott esetek

A következő szempontokat kell figyelembe venni:

- A rendszergazdák engedélyezhetik a jelszóval nem rendelkező hitelesítési módszereket a bérlők számára
- A rendszergazdák az összes felhasználót megcélozhatja, vagy kiválaszthatják a bérlőn belüli felhasználókat és csoportokat az egyes módszereknél
- A végfelhasználók a fiók-portálon regisztrálhatják és kezelhetik ezeket a jelszó nélküli hitelesítési módszereket
- A végfelhasználók a következő, jelszó nélküli hitelesítési módszerekkel jelentkezhetnek be:
   - Microsoft Authenticator alkalmazás: olyan helyzetekben működik, ahol az Azure AD-hitelesítés használatban van, beleértve az összes böngészőt, a Windows 10-es telepítése során, valamint az integrált Mobile apps-t bármely operációs rendszeren.
   - Biztonsági kulcsok: a Windows 10 és a web zárolási képernyőjén használható böngészők, például a Microsoft Edge (örökölt és új Edge).

## <a name="choose-a-passwordless-method"></a>Jelszóval nem rendelkező metódus kiválasztása

A három jelszóval nem rendelkező lehetőség közül választhat, hogy a vállalat biztonsági, platform-és alkalmazási követelményeitől függ.

Íme néhány tényező, amelyet érdemes figyelembe venni a Microsoft jelszavas technológiájának kiválasztásakor:

||**Vállalati Windows Hello**|**Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással**|**FIDO2 biztonsági kulcsok**|
|:-|:-|:-|:-|
|**Előfeltételek**| Windows 10, 1809-es vagy újabb verzió<br>Azure Active Directory| A Microsoft Authenticator alkalmazás<br>Telefon (Android 6,0 vagy újabb rendszert futtató iOS-és Android-eszközök)|Windows 10, 1903-es vagy újabb verzió<br>Azure Active Directory|
|**Mód**|Platform|Szoftverek|Hardver|
|**Rendszerek és eszközök**|Beépített platformmegbízhatósági modul (TPM) rendelkező számítógép<br>PIN-kód és biometriai felismerés |PIN-kód és biometriai felismerés telefonon|Microsoft-kompatibilis FIDO2 biztonsági eszközök|
|**Felhasználó felület**|Bejelentkezés PIN-kód vagy biometrikus felismerés (arc, írisz vagy ujjlenyomat) használatával Windows-eszközökkel.<br>A Windows Hello-hitelesítés az eszközhöz van kötve; a felhasználónak az eszközre és egy bejelentkezési összetevőre is szüksége van, például PIN-kódot vagy biometrikus tényezőt a vállalati erőforrások eléréséhez.|Jelentkezzen be egy olyan mobiltelefonnal, amely ujjlenyomat-vizsgálatot, arc-vagy írisz-felismerést vagy PIN-kódot használ.<br>A felhasználók a SZÁMÍTÓGÉPRŐL vagy a mobiltelefonjára bejelentkezhetnek a munkahelyi vagy személyes fiókba.|Bejelentkezés a FIDO2 biztonsági eszközzel (biometria, PIN-kód és NFC)<br>A felhasználó a szervezeti vezérlők és a PIN-kód alapján végzett hitelesítés alapján képes hozzáférni az eszközhöz, például az USB biztonsági kulcsok és az NFC-kompatibilis intelligens kártyák, kulcsok vagy hordható eszközök használatával.|
|**Engedélyezett forgatókönyvek**| Jelszó nélküli felhasználói élmény a Windows-eszközön.<br>A dedikált munkahelyi SZÁMÍTÓGÉPekre alkalmazható, amely képes az egyszeri bejelentkezésre az eszközre és az alkalmazásokra.|Jelszó nélküli, bárhol elérhető megoldás mobiltelefon használatával.<br>A munkahelyi vagy személyes alkalmazások webes elérésére bármely eszközről.|Jelszó nélküli felhasználói élmény a biometria, a PIN-kód és az NFC használatát végző feldolgozók számára.<br>A megosztott számítógépekre alkalmazható, és ha a mobiltelefon nem életképes megoldás (például ügyfélszolgálati munkatárs, nyilvános kioszk vagy kórházi csapat)|

A következő táblázat segítségével kiválaszthatja, hogy melyik módszer fogja támogatni a követelményeket és a felhasználókat.

|Persona|Eset|Környezet|Jelszóval nem rendelkező technológia|
|:-|:-|:-|:-|
|**Felügyelet**|Biztonságos hozzáférés egy eszközhöz felügyeleti feladatokhoz|Hozzárendelt Windows 10-es eszköz|Vállalati Windows Hello és/vagy FIDO2 biztonsági kulcs|
|**Felügyelet**|Felügyeleti feladatok nem Windows rendszerű eszközökön| Mobil-vagy nem Windows-eszköz|Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással|
|**Információkkal dolgozó feldolgozó**|Termelékenységi munka|Hozzárendelt Windows 10-es eszköz|Vállalati Windows Hello és/vagy FIDO2 biztonsági kulcs|
|**Információkkal dolgozó feldolgozó**|Termelékenységi munka| Mobil-vagy nem Windows-eszköz|Jelszó nélküli bejelentkezés a Microsoft Authenticator alkalmazással|
|**Frontline Worker**|Kioszkok gyárban, üzemben, kiskereskedelemben vagy adatbevitelben|Megosztott Windows 10-es eszközök|FIDO2 biztonsági kulcsok|

## <a name="next-steps"></a>Következő lépések

Az Azure AD-ben való jelszavas használat megkezdéséhez kövesse az alábbi útmutatók egyikét:

* [FIDO2 biztonsági kulcs jelszavas bejelentkezésének engedélyezése](howto-authentication-passwordless-security-key.md)
* [Telefonos jelszó nélküli bejelentkezés engedélyezése a hitelesítő alkalmazással](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Külső hivatkozások

* [A-Szövetség](https://fidoalliance.org/)
* [FIDO2 CTAP-specifikáció](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
