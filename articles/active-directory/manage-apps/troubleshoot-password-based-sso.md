---
title: Jelszóalapú egyszeri bejelentkezés hibaelhárítása a Azure Active Directory
description: A jelszóalapú egyszeri bejelentkezéshez konfigurált Azure AD-alkalmazásokkal kapcsolatos hibák elhárítása.
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 2d9bcbdcb292f33d4b1f9dd1d0f6779d8d6cdd49
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376477"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Jelszóalapú egyszeri bejelentkezés hibaelhárítása az Azure AD-ben

A jelszóalapú egyszeri bejelentkezés (SSO) Saját alkalmazások kell telepíteni a böngészőbővítményt. A bővítmény automatikusan letöltódik, amikor kiválaszt egy jelszóalapú SSO-hoz konfigurált alkalmazást. A Saját alkalmazások végfelhasználói szempontból való használatával kapcsolatos további információkért lásd a [Saját alkalmazások portál súgóját.](../user-help/my-apps-portal-end-user-access.md)

## <a name="my-apps-browser-extension-not-installed"></a>Saját alkalmazások böngészőbővítmény nincs telepítve
Győződjön meg arról, hogy a böngészőbővítmény telepítve van. További tudnivalókért [lásd: A](my-apps-deployment-plan.md)központi telepítés Azure Active Directory Saját alkalmazások meg. 

## <a name="single-sign-on-not-configured"></a>Az egyszeri bejelentkezés nincs konfigurálva
Győződjön meg arról, hogy a jelszóalapú egyszeri bejelentkezés be van állítva. További információ: [Jelszóalapú egyszeri bejelentkezés konfigurálása.](configure-password-single-sign-on-non-gallery-applications.md)

## <a name="users-not-assigned"></a>Nincs hozzárendelve felhasználók
Győződjön meg arról, hogy a felhasználó hozzá van rendelve az alkalmazáshoz. További információ: Felhasználó vagy csoport [hozzárendelése egy alkalmazáshoz.](assign-user-or-group-access-portal.md)

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Meg lettek adva a hitelesítő adatok, de a bővítmény nem küldi el őket

Ez a probléma általában akkor fordul elő, ha az alkalmazás forgalmazója nemrég módosította a bejelentkezési oldalát egy mező hozzáadásához, módosította a felhasználónév- és jelszómezők észleléséhez használt azonosítót, vagy módosította a bejelentkezési élmény működését az alkalmazásban. Szerencsére a Microsoft számos esetben képes az alkalmazásszállítóval együtt megoldani ezeket a problémákat.

Bár a Microsoft olyan technológiákkal rendelkezik, amelyek automatikusan észlelik, ha az integrációk megrekednek, előfordulhat, hogy nem lehet azonnal megtalálni a problémákat, vagy a problémák megoldása némi időt vehet el. Ha az integrációk valamelyike nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban kijavítható legyen.

**Ha kapcsolatba lép** az alkalmazás forgalmazójával, küldje el nekik a mi módszerünket, hogy a Microsoft együtt dolgozva natív módon integrálja az alkalmazást a Azure Active Directory. Az első lépésekhez elküldheti [a](../develop/v2-howto-app-gallery-listing.md) szállítót a Termékoldali alkalmazás Azure Active Directory az alkalmazás katalógusában.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Meg lettek adva és lettek küldve a hitelesítő adatok, de az oldal azt jelzi, hogy helytelenek

A probléma megoldásához először próbálkozzon az alábbi megoldásokkal:

- A felhasználó először  próbáljon meg közvetlenül bejelentkezni az alkalmazás webhelyére a számukra tárolt hitelesítő adatokkal.

  * Ha a bejelentkezés működik, akkor a  felhasználónak a  hitelesítő adatok  frissítése gombra kell kattintanom a Saját alkalmazások Alkalmazások szakaszában található Alkalmazás csempén a legújabb ismert felhasználónévre és jelszóra való frissítéséhez. [](https://myapps.microsoft.com/)

  * Ha Ön vagy egy másik rendszergazda hozzárendeli a felhasználó hitelesítő adatait, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését az alkalmazás Felhasználók  &-csoportok lapján, válassza **ki a** hozzárendelést, majd kattintson a Hitelesítő adatok frissítése gombra.

- Ha a felhasználó saját hitelesítő adatokat  rendelt hozzá, a felhasználónak ellenőriznie kell, hogy a jelszava nem járt-e le az **alkalmazásban,** és ha igen, frissítse a lejárt jelszót közvetlenül az alkalmazásba való bejelentkezéssel.

  * Miután frissítette a jelszót az alkalmazásban, kérje  meg a felhasználót,  hogy kattintson a [](https://myapps.microsoft.com/) Hitelesítő adatok frissítése gombra a Saját alkalmazások Alkalmazások szakaszában található Alkalmazás csempén a legújabb ismert munkafelhasználónévre és jelszóra való frissítéshez. 

  * Ha Ön vagy egy másik rendszergazda hozzárendeli a felhasználó hitelesítő adatait, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését az alkalmazás Felhasználók  **&-csoportok** lapján, válassza ki a hozzárendelést, majd kattintson a Hitelesítő adatok frissítése gombra.

- Ellenőrizze, Saját alkalmazások böngészőbővítmény fut-e és engedélyezve van-e a felhasználó böngészőjében.

- Győződjön meg arról, hogy a felhasználók nem inkognitó módban Saját alkalmazások **InPrivate** vagy Privát módban próbálnak bejelentkezni az alkalmazásba. A Saját alkalmazások bővítmény ezekben a módokban nem támogatott.

Ha az előző javaslatok nem működnek, lehetséges, hogy olyan módosítás történt az alkalmazás oldalán, amely ideiglenesen megszakadt az alkalmazás Azure AD-integrációja miatt. Ez például akkor fordulhat elő, ha az alkalmazás szállítója egy olyan szkriptet vezet be az oldalára, amely eltérően viselkedik a manuális és az automatikus bemenetek esetén, ami az automatikus integrációt , például a sajátját is megszakítja. Szerencsére a Microsoft számos esetben képes az alkalmazásszállítóval együtt megoldani ezeket a problémákat.

Bár a Microsoft rendelkezik olyan technológiákkal, amelyek automatikusan észlelik, ha az alkalmazásintegrációk megesnek, előfordulhat, hogy nem lehet azonnal megtalálni a problémákat, vagy a problémák megoldása némi időt vehet el. Ha egy integráció nem működik megfelelően, megnyithat egy támogatási esetet, hogy a lehető leggyorsabban kijavítsa. 

Emellett, ha kapcsolatba lép az alkalmazás forgalmazójával, küldje el nekik a mi módszerünket, hogy velük natív módon integrálni tudjuk az alkalmazást a Azure Active Directory.   Az első lépésekhez elküldheti [a](../develop/v2-howto-app-gallery-listing.md) szállítót a Termékoldali alkalmazás Azure Active Directory az alkalmazás katalógusában.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Ellenőrizze, hogy megváltozott-e az alkalmazás bejelentkezési oldala a közelmúltban, vagy szükség van-e további mezőre

Ha az alkalmazás bejelentkezési oldala drasztikusan változott, előfordulhat, hogy ez az integrációk feltörését okozza. Ilyen például, ha egy alkalmazásgyártó bejelentkezési mezőt, captcha-t vagy többtényezős hitelesítést ad hozzá a felhasználói élményéhez. Szerencsére a Microsoft számos esetben képes az alkalmazásszállítóval együtt megoldani ezeket a problémákat.

Bár a Microsoft rendelkezik olyan technológiákkal, amelyek automatikusan észlelik, ha az alkalmazásintegrációk megesnek, előfordulhat, hogy nem lehet azonnal megtalálni a problémákat, vagy a problémák megoldása némi időt vehet el. Ha egy integráció nem működik megfelelően, megnyithat egy támogatási esetet, hogy a lehető leggyorsabban kijavítsa. 

Emellett, ha kapcsolatba lép az alkalmazás forgalmazójával, küldje el nekik a mi módszerünket, hogy velük natív módon integrálni tudjuk az alkalmazást a Azure Active Directory.   Az első lépésekhez elküldheti [a](../develop/v2-howto-app-gallery-listing.md) szállítót a Termékoldali alkalmazás Azure Active Directory az alkalmazás katalógusában.

## <a name="capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek rögzítése

A bejelentkezési mezők rögzítése csak HTML-kompatibilis bejelentkezési oldalakon támogatott. Nem szabványos bejelentkezési oldalak esetén nem támogatott, például az Adobe Flasht vagy más nem HTML-kompatibilis technológiákat használ.

Az egyéni alkalmazások bejelentkezési mezőit kétféleképpen rögzítheti:

- **Az automatikus bejelentkezési** mezők rögzítése jól működik a legtöbb HTML-kompatibilis bejelentkezési oldalon, ha jól ismert *DIV azonosítókat* használnak a felhasználónév és a jelszó mezőkhöz. Az oldalon található HTML-kód a megadott feltételeknek megfelelő DIV-kódot keres. A rendszer menti a metaadatokat, hogy később visszajátszható legyen az alkalmazásba.

- **A rendszer manuális bejelentkezési mezők rögzítését** használja, ha az alkalmazás szállítója nem címkéz *a bejelentkezési beviteli mezőket.* A manuális rögzítést akkor is használja a rendszer, ha a szállító több olyan mezőt is megjelenít, amelyek nem *észlelhetők automatikusan.* Azure Active Directory (Azure AD) annyi mező adatait tudja tárolni, amennyire a bejelentkezési oldalon van, ha megszabadja, hogy hol vannak ezek a mezők az oldalon.

Ha az automatikus bejelentkezési mező rögzítése általában nem működik, próbálkozzon a manuális beállítással.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek automatikus rögzítése

A jelszóalapú SSO automatikus bejelentkezési mezőrögzítéssel való konfigurálásához kövesse az alábbi lépéseket:
1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társadminisztrátként.
2. A bal oldali navigációs panelen válassza a **Minden szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.
3. Írja **Azure Active Directory** a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.
4. Az  Azure AD navigációs panelen válassza a Vállalati alkalmazások lehetőséget.
5. Válassza **a Minden alkalmazás** lehetőséget az alkalmazások listájának megtekintéséhez.
   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja  a Minden alkalmazás lista tetején található **Szűrő vezérlőt.** A Show **(Megjelenítése)** beállítást állítsa "Minden alkalmazás" lehetőségre.
6. Válassza ki az SSO-hoz konfigurálni kívánt alkalmazást.
7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget a bal oldali navigációs panelen.
8. Válassza **a Jelszóalapú bejelentkezési módot.**
9. Adja meg **a Bejelentkezési URL-címet,** amely annak az oldalnak az URL-címe, amelyen a felhasználók beírják a felhasználónevüket és jelszavukat a bejelentkezéshez. *Győződjön meg arról,* hogy a bejelentkezési mezők láthatók a megadott URL-cím oldalán.
10. Kattintson a **Mentés** gombra.
    A rendszer automatikusan leküldi az oldalt a felhasználónév és a jelszó beviteli mezőinek. Mostantól az Azure AD használatával biztonságosan továbbíthat jelszavakat az alkalmazásnak a böngészőbővítmény Saját alkalmazások használatával.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek manuális rögzítése

A bejelentkezési mezők manuális rögzítéséhez telepítenie kell Saját alkalmazások böngészőbővítményt. Emellett a böngésző nem futhat *inPrivate,* *inkognitó* módban vagy *privát módban.*

Ha manuális bejelentkezési mezőrögzítéssel konfigurálja egy alkalmazás jelszóalapú SSO-ját, kövesse az alábbi lépéseket:
1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társadminisztrátként.
2. A bal oldali navigációs panelen válassza a **Minden szolgáltatás** lehetőséget az Azure AD-bővítmény megnyitásához.
3. Írja **Azure Active Directory** a szűrő keresőmezőbe, majd válassza a **Azure Active Directory.**
4. Az  Azure AD navigációs panelen válassza a Vállalati alkalmazások lehetőséget.
5. Válassza **a Minden alkalmazás** lehetőséget az alkalmazások listájának megtekintéséhez.
   > [!NOTE] 
   > Ha nem látja a kívánt alkalmazást, használja  a Minden alkalmazás lista tetején található **Szűrő vezérlőt.** A Show **(Megjelenítése)** beállítást állítsa "Minden alkalmazás" lehetőségre.
6. Válassza ki az SSO-hoz konfigurálni kívánt alkalmazást.
7. Az alkalmazás betöltése után válassza az **Egyszeri bejelentkezés** lehetőséget a bal oldali navigációs panelen.
8. Válassza **a Jelszóalapú bejelentkezési mód** lehetőséget.
9. Adja meg **a bejelentkezési URL-címet,** amely az a lap, ahol a felhasználók a felhasználónevüket és jelszavukat megadhatja a bejelentkezéshez. *Győződjön meg arról,* hogy a bejelentkezési mezők láthatók a megadott URL-cím oldalán.
10. Válassza **az *&lt; Alkalmazásnév jelszavas &gt;* egyszeri bejelentkezési beállítások konfigurálása lehetőséget.**
11. Válassza **a Bejelentkezési mezők manuális észlelése lehetőséget.**
14. Kattintson az **OK** gombra.
15. Kattintson a **Mentés** gombra.
16. Kövesse az utasításokat a Saját alkalmazások.


## <a name="troubleshoot-problems"></a>Problémák elhárítása

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>"Nem található bejelentkezési mező ezen az URL-címen" hibaüzenetet kapok

Ez a hibaüzenet jelenik meg, ha a bejelentkezési mezők automatikus észlelése sikertelen. A probléma megoldásához próbálja meg manuálisan észlelni a bejelentkezési mezőt. Lásd a cikk Alkalmazás bejelentkezési [mezőinek](#manually-capture-sign-in-fields-for-an-app) manuális rögzítése című szakaszát.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"Az egyszeri bejelentkezési konfiguráció mentése nem sikerült" hibaüzenetet kapok

Ritkán az SSO-konfiguráció frissítése sikertelen. A probléma megoldásához próbálja meg újra menteni a konfigurációt.

Ha továbbra is hibaüzenetet kap, nyisson egy támogatási esetet. Írja le a Portál értesítési részleteinek megtekintése és az [Értesítési](#send-notification-details-to-a-support-engineer-to-get-help) adatok küldése támogatási szakembernek szakaszban leírt információkat a cikk súgószakaszának lekért részében. [](#view-portal-notification-details)

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nem tudom manuálisan észlelni az alkalmazásom bejelentkezési mezőit

Ha a manuális észlelés nem működik, a következő viselkedéseket figyelheti meg:
- A manuális rögzítési folyamat működött, de a rögzített mezők helytelenek.
- A rögzítési folyamat futtatásakor a megfelelő mezők nem jelennek meg.
- A rögzítési folyamat a várt módon az alkalmazás bejelentkezési oldalára viszi, de semmi sem történik.
- Úgy tűnik, hogy a manuális rögzítés működik, de az SSO nem történik meg, amikor a felhasználók az alkalmazáshoz navigálnak a Saját alkalmazások.

Ha ezen problémák bármelyikét tapasztalja, tegye a következőket:
- Győződjön meg arról, hogy az Saját alkalmazások böngészőbővítmény legújabb verziója *van telepítve és engedélyezve.*
- A rögzítési folyamat során győződjön meg arról, hogy a  böngésző nem *inkognitó* módban, *inPrivate-ban* vagy Privát módban van. A Saját alkalmazások bővítmény ezekben a módokban nem támogatott.
- Győződjön meg arról, hogy a felhasználók nem *inkognitó* módban Saját alkalmazások *inPrivate* vagy Privát módban próbálnak bejelentkezni az *alkalmazásba.*
- Próbálkozzon újra a manuális rögzítési folyamattal. Győződjön meg arról, hogy a piros jelölők a megfelelő mezőkben vannak.
- Ha úgy tűnik, hogy a manuális rögzítési folyamat nem válaszol, vagy a bejelentkezési oldal nem válaszol, próbálkozzon újra a manuális rögzítési folyamattal. Ezúttal azonban a folyamat befejezése után nyomja le az F12 billentyűt a böngésző fejlesztői konzol megnyitásához. Válassza ki a **konzollapot.** Írja be a **window.location=" bejelentkezési *&lt; &gt; URL-címet,*** amit az alkalmazás konfigurálásakor adott meg, majd nyomja le az Enter billentyűt. Ez kényszeríti az oldal átirányítását, amely befejezi a rögzítési folyamatot, és tárolja a rögzített mezőket.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Nem tudok másik felhasználót hozzáadni a jelszóalapú SSO-alkalmazáshoz

A jelszóalapú SSO-alkalmazás korlátja 48 felhasználó. Így a felhasználónév/jelszó párok száma alkalmazásonként legfeljebb 48 kulcs lehet.
Ha további felhasználókat szeretne hozzáadni, a következő lehetőségek valamelyikét használhatja:
-   Az alkalmazás további példányának hozzáadása
-   Először távolítsa el azokat a felhasználókat, akik már nem használják az alkalmazást

## <a name="request-support"></a>Támogatás kérése 
Ha hibaüzenetet kap az SSO beállításakor és a felhasználók hozzárendelésekor, nyisson meg egy támogatási jegyet. A következő információk közül a lehető legtöbb információt foglalja magában:

-   Korrelációs hiba azonosítója
-   UPN (felhasználói e-mail-cím)
-   TenantID (Bérlőazonosító)
-   Böngésző típusa
-   A hiba beestének időzónája és időkerete
-   Fiddler-nyomkövetések

### <a name="view-portal-notification-details"></a>A portál értesítési részleteinek megtekintése

A portál értesítései részleteinek megtekintése az alábbi lépésekkel:
1. Válassza a jobb **felső** sarokban található Értesítések ikont (a harangot) Azure Portal.
2. Válassza ki a Hiba állapotot *bemutató értesítéseket.* (Piros "!".)
   > [!NOTE]
   > Nem választhatja ki a Sikeres  vagy a Folyamatban *állapotban lévő értesítéseket.*
3. Megnyílik **az Értesítés részletei** panel. A problémáról további információt itt olvashat.
5. Ha további segítségre van szüksége, ossza meg az információkat egy támogatási szakembersel vagy a termékcsoporttal. Kattintson **a Másolás** hibamező  jobb oldali másolás ikonjára az értesítés részleteinek másoláshoz a megosztáshoz.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Értesítési adatok küldése egy támogatási szakembernek segítségért

Fontos, hogy az  ebben a szakaszban felsorolt összes részletet támogatással ossza meg, hogy azok gyorsan segítséget nyújthassnak. A rögzítéséhez készíthet egy képernyőképet, vagy választhatja a **Másolási hiba lehetőséget.**

Az alábbi információk ismertetik az egyes értesítési elemek jelentését, és példákat is tartalmaznak.

#### <a name="essential-notification-items"></a>Alapvető értesítési elemek

- **Cím:** az értesítés leíró címe.

   Példa: *Alkalmazásproxy-beállítások*

- **Leírás:** a művelet eredményeként történt.

   Példa: *A megadott belső URL-címet már egy másik alkalmazás használja.*

- **Értesítési azonosító:** az értesítés egyedi azonosítója.

    Például: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Ügyfélkérés azonosítója:** a böngésző által megadott kérelemazonosító.

    Például: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Időbélyeg (UTC):** az értesítés beesésének időbélyege UTC-ben.

    Például: *2017-03-23T19:50:43.7583681Z*

- **Belső tranzakció azonosítója:** a belső azonosító, amely a hiba a rendszerekben való ki keresse.

    Például: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN:** A műveletet futtató felhasználó.

    Például: *tperkins \@ f128.info*

- **Bérlőazonosító:** annak a bérlőnek az egyedi azonosítója, amelybe a műveletet futtató felhasználó tagja.

    Például: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Felhasználói objektum azonosítója:** A műveletet futtató felhasználó egyedi azonosítója.

    Például: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Részletes értesítési elemek

- **Megjelenítendő név:**(üres is lehet) a hiba részletesebb megjelenítendő neve.

    Példa: *Alkalmazásproxy-beállítások*

- **Állapot:** az értesítés konkrét állapota.

    Például: *Sikertelen*

- **Objektumazonosító:**(üres lehet) annak az objektumazonosítónak az azonosítója, amelyen a műveletet futtatta.

   Például: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Részletek:** a művelet eredményeként történt események részletes leírása.

    Például: *A belső URL- " <https://bing.com/> érvénytelen, mert már használatban van.*

- **Másolási** hiba: Lehetővé  teszi a Másolás hiba  szövegmező jobb oldali másolás ikonjának kiválasztását az értesítés részleteinek a támogatáshoz való másolásához.

    Példa:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Következő lépések
* [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)
* [Saját alkalmazások üzembe helyezésének megtervezése](my-apps-deployment-plan.md)
