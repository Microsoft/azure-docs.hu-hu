---
title: Jelszó alapú egyszeri bejelentkezés hibáinak megoldása Azure Active Directory
description: A jelszó alapú egyszeri bejelentkezéshez konfigurált Azure AD-alkalmazással kapcsolatos problémák elhárítása.
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 84aec5031dd8d19f8f2ad0aa32185002add378b1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053979"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Jelszóalapú egyszeri bejelentkezés hibaelhárítása az Azure AD-ben

Ha jelszó-alapú egyszeri bejelentkezést (SSO) szeretne használni az alkalmazásokban, telepítenie kell a böngésző kiterjesztését. A bővítmény automatikusan töltődik le, ha olyan alkalmazást választ, amely jelszó alapú egyszeri bejelentkezéshez van konfigurálva. Ha szeretne többet megtudni az alkalmazások végfelhasználói perspektívából való használatáról, tekintse meg [az alkalmazások portál súgóját](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Nincs telepítve az alkalmazások böngésző bővítménye
Győződjön meg arról, hogy a böngésző bővítmény telepítve van. További információ: [Azure Active Directory saját alkalmazások üzembe helyezésének megtervezése](my-apps-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Az egyszeri bejelentkezés nincs konfigurálva
Győződjön meg arról, hogy a jelszó-alapú egyszeri bejelentkezés konfigurálva van. További információ: [jelszó alapú egyszeri bejelentkezés konfigurálása](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Nincs hozzárendelt felhasználó
Győződjön meg arról, hogy a felhasználó hozzá van rendelve az alkalmazáshoz. További információ: [felhasználó vagy csoport társítása egy alkalmazáshoz](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Meg lettek adva a hitelesítő adatok, de a bővítmény nem küldi el őket

Ez a probléma általában akkor fordul elő, ha az alkalmazás gyártója nemrég módosította a bejelentkezési lapját egy mező hozzáadásához, módosította a Felhasználónév és jelszó mezők észleléséhez használt azonosítót, vagy módosította, hogyan működik a bejelentkezési élmény az alkalmazáshoz. Szerencsére a Microsoft számos példányban képes együttműködni az alkalmazások forgalmazójával, hogy gyorsan megoldja ezeket a problémákat.

Habár a Microsoft olyan technológiákkal rendelkezik, amelyekkel automatikusan észlelhetők az integrációs folyamatok, előfordulhat, hogy a problémák azonnal nem találhatók meg, vagy ha a problémák kijavítása hosszabb időt vesz igénybe. Abban az esetben, ha az ilyen integrációk egyike nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban meg lehessen oldani.

**Ha kapcsolatba lép ezzel az alkalmazás forgalmazójával,** küldje el őket, így a Microsoft dolgozhat velük, hogy natív módon integrálja alkalmazásait a Azure Active Directory használatával. A szállítót elküldheti az [alkalmazásnak a Azure Active Directory alkalmazás-katalógusban](../develop/v2-howto-app-gallery-listing.md) való megjelenítéséhez, és megkezdheti őket.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Meg lettek adva és lettek küldve a hitelesítő adatok, de az oldal azt jelzi, hogy helytelenek

A probléma megoldásához először próbálja meg ezeket a dolgokat:

- Először próbáljon meg **közvetlenül bejelentkezni az alkalmazás webhelyére** a felhasználók által tárolt hitelesítő adatokkal.

  * Ha a bejelentkezés működik, a felhasználó a [saját alkalmazások](https://myapps.microsoft.com/) **alkalmazások** szakaszának az **alkalmazás csempén** a **hitelesítő adatok frissítése** gombra kattintva frissítheti azokat a legújabb ismert munkafelhasználónévre és jelszóra.

  * Ha Ön, vagy egy másik rendszergazda hozzárendelte a hitelesítő adatokat a felhasználóhoz, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését az alkalmazás **felhasználók & csoportok** lapjára kattintva, válassza ki a hozzárendelést, majd kattintson a **hitelesítő adatok frissítése** gombra.

- Ha a felhasználó hozzárendelte a saját hitelesítő adatait, a felhasználónak ellenőriznie kell, hogy a **jelszavuk nem járt-e le az alkalmazásban** , és ha igen, **frissítse a lejárt jelszót** úgy, hogy közvetlenül az alkalmazásba jelentkezik be.

  * Miután a jelszó frissítve lett az alkalmazásban, kérje meg a felhasználót, **hogy a** [saját alkalmazások](https://myapps.microsoft.com/) **alkalmazások** szakaszában kattintson a **hitelesítő adatok frissítése** gombra, és frissítse őket a legújabb ismert munkafelhasználónévre és jelszóra.

  * Ha Ön, vagy egy másik rendszergazda hozzárendelte a hitelesítő adatokat a felhasználóhoz, keresse meg a felhasználó vagy csoport alkalmazás-hozzárendelését az alkalmazás **felhasználók & csoportok** lapjára kattintva, válassza ki a hozzárendelést, majd kattintson a **hitelesítő adatok frissítése** gombra.

- Győződjön meg arról, hogy a saját alkalmazások böngésző bővítmény fut, és engedélyezve van a felhasználó böngészőjében.

- Győződjön meg arról, hogy a felhasználók nem próbálnak bejelentkezni az alkalmazásokba az alkalmazásból az **Incognito, InPrivate vagy Private módban**. A saját alkalmazások bővítmény nem támogatott ezekben a módokban.

Abban az esetben, ha az előző javaslatok nem működnek, lehet, hogy változás történt az alkalmazás oldalán, amely átmenetileg megszakította az alkalmazás Azure AD-integrációját. Ez például akkor fordulhat elő, ha az alkalmazás gyártója olyan parancsfájlt vezet be a lapon, amely másképp viselkedik a kézi és az automatikus bevitel esetében, ami az automatikus integrációt eredményezi, mint a saját, a törés. Szerencsére a Microsoft számos példányban képes együttműködni az alkalmazások forgalmazójával, hogy gyorsan megoldja ezeket a problémákat.

Habár a Microsoft olyan technológiákkal rendelkezik, amelyekkel automatikusan észlelhetők az alkalmazások integrációja, előfordulhat, hogy nem lehet azonnal megkeresni a problémákat, vagy ha a problémák egy ideig eltartanak. Ha egy integráció nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban rögzítse. 

Ezen kívül, **Ha kapcsolatba lép ezzel az alkalmazás forgalmazójával,** **küldje** el a módját, hogy natív módon integrálja alkalmazásait a Azure Active Directory használatával. A szállítót elküldheti az [alkalmazásnak a Azure Active Directory alkalmazás-katalógusban](../develop/v2-howto-app-gallery-listing.md) való megjelenítéséhez, és megkezdheti őket.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Ellenőrizze, hogy az alkalmazás bejelentkezési lapja nemrég módosult-e, vagy további mezőt igényel

Ha az alkalmazás bejelentkezési lapja drasztikusan módosult, időnként az integrációnk megszakad. Ilyen lehet például, ha egy alkalmazás gyártója felvesz egy bejelentkezési mezőt, egy CAPTCHA-t vagy egy többtényezős hitelesítést a saját tapasztalataiba. Szerencsére a Microsoft számos példányban képes együttműködni az alkalmazások forgalmazójával, hogy gyorsan megoldja ezeket a problémákat.

Habár a Microsoft olyan technológiákkal rendelkezik, amelyekkel automatikusan észlelhetők az alkalmazások integrációja, előfordulhat, hogy nem lehet azonnal megkeresni a problémákat, vagy ha a problémák egy ideig eltartanak. Ha egy integráció nem működik megfelelően, nyisson meg egy támogatási esetet, hogy a lehető leggyorsabban rögzítse. 

Ezen kívül, **Ha kapcsolatba lép ezzel az alkalmazás forgalmazójával,** **küldje** el a módját, hogy natív módon integrálja alkalmazásait a Azure Active Directory használatával. A szállítót elküldheti az [alkalmazásnak a Azure Active Directory alkalmazás-katalógusban](../develop/v2-howto-app-gallery-listing.md) való megjelenítéséhez, és megkezdheti őket.

## <a name="capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek rögzítése

A bejelentkezési mezők rögzítése csak HTML-kompatibilis bejelentkezési lapok esetén támogatott. A nem szabványos bejelentkezési lapok esetében nem támogatottak, például az Adobe Flash vagy más, nem HTML-kompatibilis technológiák használatát.

Az egyéni alkalmazások bejelentkezési mezőinek rögzítése kétféleképpen lehetséges:

- *Ha jól ismert div-azonosítókat használ* a Felhasználónév és a jelszó mezőkhöz, az **automatikus bejelentkezési mező rögzítése** a legtöbb HTML-kompatibilis bejelentkezési oldallal jól működik. Az oldalon található HTML-fájl a megadott feltételeknek megfelelő DIV-azonosítók megkeresésére van lekaparva. A rendszer menti a metaadatokat, hogy később újra lehessen játszani az alkalmazásban.

- A **bejelentkezési mező manuális rögzítése** akkor használatos, ha az alkalmazás gyártója *nem címkézi a bejelentkezési beviteli mezőket*. A manuális rögzítés akkor is használatos, ha a szállító *több olyan mezőt jelenít meg, amelyek nem észlelhetők automatikusan*. A Azure Active Directory (Azure AD) annyi mezőre képes tárolni az adattárolást, mint a bejelentkezési oldalon, ha azt mondja, hogy ezek a mezők az oldalon találhatók.

Általánosságban elmondható, hogy ha az automatikus bejelentkezési mező rögzítése nem működik, próbálkozzon a manuális beállítással.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Alkalmazás bejelentkezési mezőinek automatikus rögzítése

Ha a jelszó-alapú egyszeri bejelentkezést az automatikus bejelentkezési mező rögzítése használatával szeretné konfigurálni, kövesse az alábbi lépéseket:
1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.
2. A bal oldali navigációs ablaktáblán a **minden szolgáltatás** elemre kattintva nyissa meg az Azure ad bővítményt.
3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.
4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.
5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.
   > [!NOTE]
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás** lista tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.
6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.
7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablaktáblán.
8. Válassza a **jelszó alapú bejelentkezési** mód lehetőséget.
9. Adja meg a **bejelentkezési URL-címet**, amely annak az oldalnak az URL-címe, ahol a felhasználók megadják a felhasználónevét és jelszavát a bejelentkezéshez. Győződjön *meg arról, hogy a bejelentkezési mezők láthatók az Ön által megadott URL-címen a lapon*.
10. Kattintson a **Mentés** gombra.
    A rendszer automatikusan lekaparja a lapot a Felhasználónév és a jelszó beviteli mezőihez. Mostantól az Azure AD használatával biztonságosan továbbíthatja a jelszavakat az alkalmazásba a saját alkalmazások böngésző bővítménnyel.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Egy alkalmazás bejelentkezési mezőinek manuális rögzítése

A bejelentkezési mezők manuális rögzítéséhez telepíteni kell a saját alkalmazások böngésző bővítményét. Emellett a böngésző nem futhat *InPrivate*, *inkognitóban* vagy *privát* módban.

Ha a jelszó-alapú egyszeri bejelentkezést szeretné beállítani egy alkalmazáshoz a manuális bejelentkezési mező rögzítése segítségével, kövesse az alábbi lépéseket:
1. Nyissa meg az [Azure Portalt](https://portal.azure.com/). Jelentkezzen be globális rendszergazdaként vagy társ-rendszergazdaként.
2. A bal oldali navigációs ablaktáblán a **minden szolgáltatás** elemre kattintva nyissa meg az Azure ad bővítményt.
3. Írja be a **Azure Active Directory** kifejezést a szűrő keresőmezőbe, majd válassza a **Azure Active Directory** lehetőséget.
4. Válassza a **vállalati alkalmazások** lehetőséget az Azure ad navigációs ablaktábláján.
5. Az alkalmazások listájának megtekintéséhez kattintson a **minden alkalmazás** elemre.
   > [!NOTE] 
   > Ha nem látja a kívánt alkalmazást, használja a **minden alkalmazás** lista tetején található **szűrő** vezérlőelemet. Állítsa a **show (megjelenítés** ) lehetőséget a "minden alkalmazás" értékre.
6. Válassza ki az egyszeri bejelentkezéshez konfigurálni kívánt alkalmazást.
7. Az alkalmazás betöltése után válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali navigációs ablaktáblán.
8. Válassza a **jelszó alapú bejelentkezési** mód lehetőséget.
9. Adja meg a **bejelentkezési URL-címet**, amely a felhasználók felhasználónevének és jelszavának megadására szolgáló lap. Győződjön *meg arról, hogy a bejelentkezési mezők láthatók az Ön által megadott URL-címen a lapon*.
10. Válassza **a *&lt; AppName &gt;* jelszavának egyszeri bejelentkezési beállításainak konfigurálása** lehetőséget.
11. Jelölje be **a bejelentkezési mezők manuális észlelése** jelölőnégyzetet.
14. Kattintson az **OK** gombra.
15. Kattintson a **Mentés** gombra.
16. Az alkalmazások használatához kövesse az utasításokat.


## <a name="troubleshoot-problems"></a>Problémák elhárítása

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>"Nem találtunk a bejelentkezési mezőket ezen az URL-címen" hibaüzenet jelenik meg.

Ez a hibaüzenet akkor jelenik meg, ha a bejelentkezési mezők automatikus észlelése sikertelen. A probléma megoldásához próbálkozzon a bejelentkezési mezők manuális észlelésével. Tekintse meg a jelen cikk [alkalmazás-és bejelentkezési mezőinek kézi rögzítése](#manually-capture-sign-in-fields-for-an-app) című szakaszát.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"Nem sikerült menteni az egyszeri bejelentkezési konfigurációt" hibaüzenet jelenik meg

Ritkán, az SSO-konfiguráció frissítése sikertelen. A probléma megoldásához próbálja meg újból menteni a konfigurációt.

Ha megtartja a hibát, nyisson meg egy támogatási esetet. A jelen cikk Súgó szakaszának beszerzéséhez adja meg a [portál értesítési adatainak megtekintése](#view-portal-notification-details) és az [értesítési adatok küldése támogatási szakembernek](#send-notification-details-to-a-support-engineer-to-get-help) című témakörben leírt információkat.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Nem tudom manuálisan megállapítani a bejelentkezési mezőket az alkalmazáshoz

Ha a manuális észlelés nem működik, a következő viselkedéseket érdemes figyelembe venni:
- A manuális rögzítési folyamat úgy tűnt, hogy működik, de a rögzített mezők nem megfelelőek.
- A rögzítési folyamat futtatásakor a megfelelő mezők nem jelennek meg a kiemelve.
- A rögzítési folyamat végigvezeti az alkalmazás bejelentkezési oldalának a várt módon, de semmi nem történik.
- A manuális rögzítés úgy tűnik, hogy működik, az egyszeri bejelentkezés azonban nem történik meg, amikor a felhasználók az alkalmazásból navigálnak az alkalmazáshoz.

Ha ezeket a problémákat tapasztalja, tegye a következőket:
- Győződjön meg arról, hogy a saját alkalmazások böngésző bővítményének legújabb verziója van *telepítve és engedélyezve*.
- A rögzítési folyamat során győződjön meg arról, hogy a böngésző nem az *inkognitóban*, *InPrivate* vagy *Private* módban van. A saját alkalmazások bővítmény nem támogatott ezekben a módokban.
- Győződjön meg arról, hogy a felhasználók nem próbálnak bejelentkezni az alkalmazásokba az alkalmazásból az *Incognito*, *InPrivate* vagy *Private módban*.
- Próbálkozzon újra a manuális rögzítési folyamattal. Győződjön meg arról, hogy a piros jelölők a megfelelő mezőkön vannak.
- Ha a manuális rögzítési folyamat úgy tűnik, hogy nem válaszol, vagy a bejelentkezési oldal nem válaszol, próbálkozzon újra a manuális rögzítési folyamattal. Ezúttal azonban a folyamat befejezése után nyomja le az F12 billentyűt a böngésző fejlesztői konzoljának megnyitásához. Válassza a **konzol** fület. írja be az **ablakot. location = "*&lt; az alkalmazás &gt; konfigurálásakor megadott bejelentkezési URL-címet***, majd nyomja le az ENTER billentyűt. Ez kényszeríti az átirányítást, amely befejezi a rögzítési folyamatot, és a rögzített mezőket tárolja.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Nem tudok másik felhasználót felvenni a jelszó-alapú SSO-alkalmazásba

A jelszó-alapú SSO-alkalmazás legfeljebb 48 felhasználóval rendelkezik. Így az alkalmazásban legfeljebb 48 kulcs adható meg a username/Password pár számára.
Ha további felhasználókat szeretne hozzáadni, akkor a következők közül választhat:
-   Az alkalmazás további példányának hozzáadása
-   Távolítsa el azokat a felhasználókat, akik már nem használják az alkalmazást

## <a name="request-support"></a>Támogatás kérése 
Ha az egyszeri bejelentkezés beállítása és a felhasználók kiosztása során hibaüzenet jelenik meg, nyisson meg egy támogatási jegyet. A lehető legtöbbet a következő információkból állhat:

-   Korrelációs hiba azonosítója
-   UPN (felhasználói e-mail-cím)
-   TenantID
-   Böngésző típusa
-   Az időzóna és az idő/idő keret a hiba bekövetkezésekor
-   Hegedűs nyomkövetései

### <a name="view-portal-notification-details"></a>A portál értesítési adatainak megtekintése

A portálon megjelenő értesítések részleteinek megtekintéséhez kövesse az alábbi lépéseket:
1. Válassza ki az **értesítések** ikont (a harangot) a Azure Portal jobb felső sarkában.
2. Válassza ki a *hibás* állapotot megjelenítő értesítéseket. (Piros "!".)
   > [!NOTE]
   > Nem választhat olyan értesítéseket, amelyek *sikeres* vagy *folyamatban* lévő állapotban vannak.
3. Megnyílik az **értesítés részletei** ablaktábla. A problémával kapcsolatos további információkért olvassa el a következő témakört:.
5. Ha továbbra is segítségre van szüksége, ossza meg az információkat egy támogatási szakemberrel vagy a termék csoporttal. Válassza a másolási **hiba** mező jobb oldalán található **Másolás** ikont az értesítési adatok megosztásra másolásához.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Értesítési adatok küldése támogatási szakembernek segítség kéréséhez

Fontos, hogy az ebben a szakaszban felsorolt *összes* részletet a támogatással ossza meg, így gyorsan segíthet. A rögzítéshez képernyőfelvételt készíthet, vagy kiválaszthatja a **másolási hibát**.

Az alábbi információk ismertetik, hogy az egyes értesítési elemek mit jelentenek és példákat biztosítanak.

#### <a name="essential-notification-items"></a>Alapvető értesítési elemek

- **Title**: az értesítés leíró címe.

   Példa: *alkalmazásproxy-beállítások*

- **Leírás**: mi történt a művelet eredményeként.

   Példa: *a megadott belső URL-címet már egy másik alkalmazás használja.*

- **Értesítés azonosítója**: az értesítés egyedi azonosítója.

    Példa: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Ügyfél-kérelem azonosítója**: a böngésző által megadott kérelem-azonosító.

    Példa: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Időbélyeg UTC**: az értesítés időbélyegzője, UTC szerint.

    Példa: *2017-03-23T19:50:43.7583681 z*

- **Belső tranzakció azonosítója**: az a belső azonosító, amelyet a rendszer a hibájának keresésére használ.

    Példa: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: az a felhasználó, aki futtatta a műveletet.

    Példa: *tperkins \@ f128.info*

- **Bérlő azonosítója**: annak a bérlőnek az egyedi azonosítója, amely a műveletet futtató felhasználó tagja.

    Példa: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Felhasználói objektum azonosítója**: a műveletet futtató felhasználó egyedi azonosítója.

    Példa: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Részletes értesítési elemek

- **Megjelenített név**: (üres is lehet) a hiba részletesebb megjelenítendő neve.

    Példa: *alkalmazásproxy-beállítások*

- **Állapot**: az értesítés konkrét állapota.

    Példa: *sikertelen*

- **Objektumazonosító**: (üres is lehet) a művelet futtatására szolgáló objektumazonosító.

   Példa: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Részletek**: a művelet eredményének részletes leírása.

    Példa: a *belső URL-cím <https://bing.com/> érvénytelen, mert már használatban van.*

- **Másolási hiba**: lehetővé teszi, hogy a másolási **hiba** szövegmező jobb oldalán válassza ki a **Másolás ikont** az értesítési adatok másolásához, hogy segítsen a támogatásban.

    Például   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Következő lépések
* [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
* [Saját alkalmazások üzembe helyezésének megtervezése](my-apps-deployment-plan.md)
