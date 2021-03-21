---
title: Önkiszolgáló jelszó-visszaállítás – Azure Active Directory
description: Ismerje meg, hogyan lehet elhárítani a gyakori problémákat és a megoldási lépéseket az önkiszolgáló jelszó-visszaállítás Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dee21182349108c44f9d498417d3b760ac4913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600862"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Az önkiszolgáló jelszó-visszaállítás hibáinak megoldása Azure Active Directory

Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára, hogy a felhőben visszaállítsa a jelszavukat.

Ha problémái vannak az SSPR-mel, a következő hibaelhárítási lépések és gyakori hibák segíthetnek. Ezt a rövid videót is megnézheti, [hogyan oldja meg a hat leggyakoribb SSPR végfelhasználói hibaüzeneteket](https://www.youtube.com/watch?v=9RPrNVLzT8I&list=PL3ZTgFEc7LyuS8615yo39LtXR7j1GCerW&index=1).

Ha nem találja a választ a problémájára, a [támogatási csapatunk mindig](#contact-microsoft-support) szívesen segít Önnek.

## <a name="sspr-configuration-in-the-azure-portal"></a>SSPR-konfiguráció a Azure Portal

Ha problémák merülnek fel a Azure Portal SSPR-beállításainak megtekintésével vagy konfigurálásával kapcsolatban, tekintse át a következő hibaelhárítási lépéseket:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Nem jelenik meg a **jelszó-visszaállítás** szakasz az Azure ad-ben a Azure Portal.

Ha nem rendelkezik olyan Azure AD-licenccel, amely a műveletet végző rendszergazdához tartozik, nem jelenik meg a **jelszó-visszaállítási** menü.

Ha a szóban forgó rendszergazdai fiókhoz szeretne licencet rendelni, kövesse a [licencekkel kapcsolatos problémák hozzárendelésének, ellenőrzésének és megoldásának](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)lépéseit.

### <a name="i-dont-see-a-particular-configuration-option"></a>Nem látok egy adott konfigurációs beállítást.

A felhasználói felület számos eleme rejtve marad, amíg nincs rájuk szükség. Győződjön meg arról, hogy a beállítás engedélyezve van, mielőtt megkeresi az adott konfigurációs beállításokat.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Nem látom a helyszíni **integráció** lapot.

A helyszíni jelszó visszaírási csak akkor látható, ha letöltötte Azure AD Connect és konfigurálta a szolgáltatást.

További információ: [Bevezetés a Azure ad Connect](../hybrid/how-to-connect-install-express.md)használatába.

## <a name="sspr-reporting"></a>SSPR-jelentéskészítés

Ha a Azure Portal SSPR-jelentéskészítési problémái vannak, tekintse át a következő hibaelhárítási lépéseket:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>Nem látom a jelszó-kezelési tevékenységek típusát az **önkiszolgáló jelszó-kezelés** naplózási esemény kategóriában.

Ez akkor fordulhat elő, ha nem rendelkezik a műveletet végző rendszergazdához rendelt Azure AD-licenccel.

Ha a szóban forgó rendszergazdai fiókhoz szeretne licencet rendelni, kövesse a [licencekkel kapcsolatos problémák hozzárendelésének, ellenőrzésének és megoldásának](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)lépéseit.

### <a name="user-registrations-show-multiple-times"></a>A felhasználói regisztráció többször is megjelenik.

Amikor egy felhasználó regisztrálja, a rendszer jelenleg a különálló eseményként regisztrált egyes adatmennyiségeket naplózza.

Ha szeretné összesíteni ezeket az adatokat, és nagyobb rugalmasságot szeretne megtekinteni, töltse le a jelentést, és nyissa meg az adatokat pivot táblázatként az Excelben.

## <a name="sspr-registration-portal"></a>SSPR regisztrációs portál

Ha a felhasználók nem sikerül regisztrálni a SSPR, tekintse át a következő hibaelhárítási lépéseket:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>A könyvtár nincs engedélyezve a jelszó-visszaállításhoz. A felhasználó a következő hibaüzenetet kaphatja: "a rendszergazda nem engedélyezte a funkció használatát."

Engedélyezheti a SSPR az összes felhasználó, a felhasználók és a felhasználók kiválasztott csoportjai számára. Jelenleg csak egy Azure AD-csoport engedélyezhető a SSPR a Azure Portal használatával. A SSPR szélesebb körű üzembe helyezésének részeként beágyazott csoportok is támogatottak. Győződjön meg arról, hogy a kiválasztott csoport (ok) hoz tartozó felhasználók rendelkeznek a megfelelő licencekkel.

A Azure Portalban módosítsa az **önkiszolgáló jelszó-visszaállítás engedélyezett** konfigurációját a *kiválasztott* vagy *az összes* értékre, majd válassza a **Mentés** lehetőséget.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>A felhasználónak nincs hozzárendelt Azure AD-licence. A felhasználó a következő hibaüzenetet kaphatja: "a rendszergazda nem engedélyezte a funkció használatát."

Jelenleg csak egy Azure AD-csoport engedélyezhető a SSPR a Azure Portal használatával. A SSPR szélesebb körű üzembe helyezésének részeként beágyazott csoportok is támogatottak. Győződjön meg arról, hogy a kiválasztott csoport (ok) hoz tartozó felhasználók rendelkeznek a megfelelő licencekkel. Tekintse át az előző hibaelhárítási lépést, hogy szükség esetén engedélyezze a SSPR.

Tekintse át a hibaelhárítási lépéseket is, hogy megbizonyosodjon róla, hogy a konfigurációs beállításokat végrehajtó rendszergazda hozzárendelt licenccel rendelkezik. Ha a szóban forgó rendszergazdai fiókhoz szeretne licencet rendelni, kövesse a [licencekkel kapcsolatos problémák hozzárendelésének, ellenőrzésének és megoldásának](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)lépéseit.

### <a name="theres-an-error-processing-the-request"></a>Hiba történt a kérelem feldolgozásakor.

Az általános SSPR-regisztrációs hibákat számos probléma okozhatja, de ezt a hibát általában egy szolgáltatás kimaradása vagy egy konfigurációs probléma okozza. Ha továbbra is ezt az általános hibát látja, amikor újra próbálkozik a SSPR regisztrációs folyamatával, további segítségért [forduljon a Microsoft támogatási szolgálatához](#contact-microsoft-support) .

## <a name="sspr-usage"></a>SSPR-használat

Ha Ön vagy a felhasználók problémákat tapasztalnak a SSPR használatával, tekintse át a következő hibaelhárítási forgatókönyveket és a megoldás lépéseit:

| Hiba | Megoldás |
| --- | --- |
| A könyvtár nincs engedélyezve a jelszó-visszaállításhoz. | A Azure Portalban módosítsa az **önkiszolgáló jelszó-visszaállítás engedélyezett** konfigurációját a *kiválasztott* vagy *az összes* értékre, majd válassza a **Mentés** lehetőséget. |
| A felhasználónak nincs hozzárendelt Azure AD-licence. | Ez akkor fordulhat elő, ha nem rendelkezik a kívánt felhasználóhoz hozzárendelt Azure AD-licenccel. Ha a szóban forgó rendszergazdai fiókhoz szeretne licencet rendelni, kövesse a [licencekkel kapcsolatos problémák hozzárendelésének, ellenőrzésének és megoldásának](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses)lépéseit. |
| A könyvtár engedélyezve van a jelszó-visszaállításhoz, de a felhasználó hiányzó vagy helytelenül formázott hitelesítési adatokat tartalmaz. | Győződjön meg arról, hogy a felhasználó a címtárban megfelelően alakította ki a kapcsolattartási adatfájlokat. További információ: az [Azure ad önkiszolgáló jelszó-visszaállítási szolgáltatás által használt adatok](howto-sspr-authenticationdata.md). |
| A könyvtár engedélyezve van a jelszó-visszaállításhoz, de a felhasználónak csak egy adatkapcsolati adata van a fájlon, ha a házirend úgy van beállítva, hogy két ellenőrzési módszert igényeljen. | Győződjön meg arról, hogy a felhasználó legalább két megfelelően konfigurált kapcsolattartási módszerrel rendelkezik. Ilyen például a mobiltelefon *-szám és* az Office-telefonszám is. |
| A könyvtár engedélyezve van a jelszó-visszaállításhoz, és a felhasználó megfelelően van konfigurálva, de a felhasználó nem tud kapcsolatba lépni vele. | Ez lehet egy ideiglenes szolgáltatási hiba eredménye, vagy ha helytelen a kapcsolattartási adatmennyiség, amelyet nem tudunk megfelelően észlelni. <br> <br> Ha a felhasználó 10 másodpercet vár, megjelenik egy hivatkozás, amely "próbálkozzon újra" és "a rendszergazda segítségére" üzenet jelenik meg. Ha a felhasználó az "próbálja újra" lehetőséget választja, az újrapróbálkozik a hívással. Ha a felhasználó a "Kapcsolatfelvétel a rendszergazdával" lehetőséget választja, a rendszer e-mailt küld a rendszergazdáknak, amely az adott felhasználói fiók jelszavának visszaállítását kéri. |
| A felhasználó soha nem fogadja a jelszó-visszaállítás SMS-t vagy telefonhívást. | Ez lehet egy helytelenül formázott telefonszám eredménye a címtárban. Győződjön meg arról, hogy a telefonszám a "+ 1 4251234567" formátumban van. <br> <br>A jelszó-visszaállítás nem támogatja a bővítményeket, még akkor sem, ha a címtárban megad egyet. A bővítmények a hívás előtt el lesznek bocsátva. Használjon kiterjesztés nélküli számot, vagy integrálja a bővítményt a privát fiókirodában (PBX) lévő telefonszámba. |
| A felhasználó soha nem kapja meg a jelszó-visszaállítási e-mailt. | Ennek a problémának a leggyakoribb oka az, hogy az üzenetet egy levélszemét-szűrő elutasítja. A levélszemét, a levélszemét vagy a törölt elemek mappa megkeresése az e-mailben. <br> <br> Győződjön meg arról is, hogy a felhasználó ellenőrzi a megfelelő e-mail-fiókot a SSPR-ben regisztráltként. |
| Beállítottam egy jelszó-visszaállítási házirendet, de ha egy rendszergazdai fiók jelszó-visszaállítást használ, a házirend nincs alkalmazva. | A Microsoft felügyeli és szabályozza a rendszergazdai jelszó-visszaállítási házirendet a legmagasabb szintű biztonság érdekében. |
| A felhasználó nem próbálkozik túl sokszor a jelszó átállítására egy napon belül. | Az automatikus szabályozási mechanizmus segítségével letilthatja, hogy a felhasználók túl sokszor próbálják meg visszaállítani a jelszavukat. A szabályozás a következő esetekben fordul elő: <br><ul><li>A felhasználó egy órán belül ötször próbálkozik a telefonszám ellenőrzésével.</li><li>A felhasználó ötször kísérli meg a biztonsági kérdések kapujának használatát egy órán belül.</li><li>A felhasználó egy órán belül ötször próbálkozik ugyanazon felhasználói fiók jelszavának alaphelyzetbe állításával.</li></ul>Ha a felhasználó ezt a problémát tapasztalja, a legutóbbi kísérlet után 24 órával várnia kell. A felhasználó ezután alaphelyzetbe állíthatja a jelszavát. |
| A felhasználó a telefonszáma ellenőrzésekor hibaüzenetet kap. | Ez a hiba akkor fordul elő, ha a beírt telefonszám nem egyezik meg a fájl telefonszámával. Győződjön meg arról, hogy a felhasználó beírja a teljes telefonszámot, beleértve a körzetet és az országkódot is, amikor telefonos alapú módszert próbál használni a jelszó-visszaállításhoz. |
| Hiba történt a kérelem feldolgozásakor. | Az általános SSPR-regisztrációs hibákat számos probléma okozhatja, de ezt a hibát általában egy szolgáltatás kimaradása vagy egy konfigurációs probléma okozza. Ha továbbra is ezt az általános hibát látja, amikor újra próbálkozik a SSPR regisztrációs folyamatával, további segítségért [forduljon a Microsoft támogatási szolgálatához](#contact-microsoft-support) . |
| Helyszíni szabályzat megsértése | A jelszó nem felel meg a helyszíni Active Directory jelszavának. A felhasználónak meg kell adnia egy jelszót, amely megfelel az összetettségi vagy erősségi követelményeknek. |
| A jelszó nem felel meg a homályos házirendnek | A használt jelszó megjelenik a [tiltott jelszavak listájában](./concept-password-ban-bad.md#how-are-passwords-evaluated) , és nem használható. A felhasználónak meg kell adnia egy jelszót, amely megfelel vagy meghaladja a tiltott jelszó-lista házirendjét. |

## <a name="sspr-errors-that-a-user-might-see"></a>A felhasználók által látható SSPR hibák

A SSPR folyamat részeként a következő hibák és technikai részletek jelenhetnek meg a felhasználó számára. Gyakran előfordul, hogy a hiba nem maga oldja meg a hibát, mivel a SSPR funkciónak engedélyezve, konfigurálva vagy regisztrálva kell lennie a fiókhoz.

Az alábbi információk segítségével megismerheti a problémát, és azt, hogy mit kell kijavítani az Azure AD-bérlőn vagy az egyedi felhasználói fiókon.

| Hiba | Részletek | Technikai részletek |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | A jelszó jelenleg nem állítható alaphelyzetbe, mert a rendszergazda letiltotta a jelszó-visszaállítást a szervezet számára. Ez a helyzet nem oldható fel további művelettel. Forduljon a rendszergazdához, és kérje meg őket, hogy engedélyezze ezt a funkciót.<br /><br />További információ: [Súgó, elfelejtettem az Azure ad-jelszavam](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: azt észlelte, hogy a rendszergazda nem engedélyezte a jelszó-visszaállítást. Forduljon a rendszergazdához, és kérje meg őket, hogy engedélyezze a jelszó-visszaállítást a szervezet számára. |
| WritebackNotEnabled = 10 |Sajnos nem lehet alaphelyzetbe állítani a jelszót, mert a rendszergazda nem engedélyezte a szükséges szolgáltatást a szervezet számára. Ez a helyzet nem oldható fel további művelettel. Forduljon a rendszergazdához, és kérje meg, hogy ellenőrizze a szervezet konfigurációját.<br /><br />További információ a szükséges szolgáltatásról: a [jelszó visszaírási beállítása](./tutorial-enable-sspr-writeback.md). | SSPR_0010: a rendszer azt észlelte, hogy a jelszó visszaírási nincs engedélyezve. Forduljon a rendszergazdához, és kérje meg, hogy engedélyezze a jelszó-visszaírási. |
| SsprNotEnabledInUserPolicy = 11 | Sajnos nem lehet alaphelyzetbe állítani a jelszót, mert a rendszergazda nem konfigurálta a jelszó-visszaállítást a szervezet számára. Ez a helyzet nem oldható fel további művelettel. Forduljon a rendszergazdához, és kérje meg őket, hogy konfigurálja a jelszó-visszaállítást.<br /><br />A jelszó-visszaállítási konfigurációval kapcsolatos további tudnivalókért lásd: gyors útmutató [: Azure ad önkiszolgáló jelszó-visszaállítás](./tutorial-enable-sspr.md). | SSPR_0011: a szervezet nem definiált jelszó-visszaállítási házirendet. Forduljon a rendszergazdához, és kérje meg őket, hogy definiálják a jelszó-visszaállítási szabályzatot. |
| UserNotLicensed = 12 | Sajnos nem lehet alaphelyzetbe állítani a jelszavát, mert a szükséges licencek hiányoznak a szervezetből. Ez a helyzet nem oldható fel további művelettel. Forduljon a rendszergazdához, és kérje meg, hogy ellenőrizze a licenc-hozzárendelését.<br /><br />A licenceléssel kapcsolatos további tudnivalókért tekintse meg [Az Azure ad önkiszolgáló jelszó-visszaállítás licencelési követelményeit](./concept-sspr-licensing.md)ismertető témakört. | SSPR_0012: a szervezet nem rendelkezik a jelszó-visszaállítás végrehajtásához szükséges licenccel. Forduljon a rendszergazdához, és kérje meg őket, hogy tekintsék át a licenc-hozzárendeléseket. |
| UserNotMemberOfScopedAccessGroup = 13 | Sajnos nem lehet alaphelyzetbe állítani a jelszót, mert a rendszergazda nem konfigurálta a fiókját a jelszó-visszaállítás használatára. Ez a helyzet nem oldható fel további művelettel. Forduljon a rendszergazdához, és kérje meg, hogy konfigurálja a fiókját a jelszó-visszaállításhoz.<br /><br />A jelszó-visszaállítási fiók konfigurálásával kapcsolatos további tudnivalókért lásd: [jelszavak alaphelyzetbe állítása a felhasználók](./howto-sspr-deployment.md)számára. | SSPR_0013: nem tagja a jelszó-visszaállításhoz engedélyezett csoportnak. Forduljon a rendszergazdához, és kérje, hogy adja hozzá a csoportot. |
| UserNotProperlyConfigured = 14 | Sajnos nem lehet alaphelyzetbe állítani a jelszavát, mert a szükséges információk hiányoznak a fiókjából. Ez a helyzet nem oldható fel további művelettel. Forduljon a rendszergazdához, és kérje meg őket, hogy állítsa alaphelyzetbe a jelszót. Miután ismét hozzáfér a fiókjához, regisztrálnia kell a szükséges információkat.<br /><br />Az információk regisztrálásához kövesse a [regisztrálás az önkiszolgáló jelszó-visszaállításhoz](../user-help/active-directory-passwords-reset-register.md) című cikket. | SSPR_0014: a jelszó alaphelyzetbe állításához további biztonsági adatokra van szükség. A folytatáshoz forduljon a rendszergazdához, és kérje meg őket, hogy állítsa alaphelyzetbe a jelszót. Ha hozzáfér a fiókjához, további biztonsági adatokat is regisztrálhat a következő címen: https://aka.ms/ssprsetup . A rendszergazda további biztonsági adatokat adhat a fiókjához a [jelszó-visszaállításra vonatkozó beállítás és olvasás hitelesítési adatok megadása](howto-sspr-authenticationdata.md)című témakör lépéseit követve. |
| OnPremisesAdminActionRequired = 29 | Sajnos nem lehet alaphelyzetbe állítani a jelszavát, mert probléma van a szervezete jelszó-visszaállítási konfigurációjával. Ez a helyzet nem oldható fel további művelettel. Forduljon a rendszergazdához, és kérje meg, hogy vizsgálja meg őket. <br /><br />Vagy<br /><br />Jelenleg nem lehet alaphelyzetbe állítani a jelszavát, mert probléma van a szervezete jelszó-visszaállítási konfigurációjával. A probléma megoldásához nem végezhető el további művelet. Forduljon a rendszergazdához, és kérje meg, hogy vizsgálja meg őket.<br /><br />Ha többet szeretne megtudni a lehetséges problémákról, tekintse meg a [jelszó-visszaírási elhárítása](troubleshoot-sspr-writeback.md)című témakört. | SSPR_0029: nem lehet alaphelyzetbe állítani a jelszót a helyszíni konfiguráció hibája miatt. Forduljon a rendszergazdához, és kérje meg, hogy vizsgálja meg őket. |
| OnPremisesConnectivityError = 30 | Sajnos nem lehet alaphelyzetbe állítani a jelszavát, mert a kapcsolati problémái vannak a szervezete számára. Jelenleg nem kell végrehajtania a műveletet, de a probléma feloldható, ha később próbálkozik. Ha a probléma továbbra is fennáll, forduljon a rendszergazdához, és kérje meg, hogy vizsgálja meg őket.<br /><br />A csatlakozási problémákkal kapcsolatos további információkért lásd: [jelszó-visszaírási kapcsolat hibaelhárítása](troubleshoot-sspr-writeback.md). | SSPR_0030: a jelszót nem lehet alaphelyzetbe állítani a helyszíni környezettel való rossz kapcsolódás miatt. Forduljon a rendszergazdához, és kérje meg, hogy vizsgálja meg őket.|

## <a name="azure-ad-forums"></a>Azure AD-fórumok

Ha általános kérdései vannak az Azure AD-vel és az önkiszolgáló jelszó-visszaállítással kapcsolatban, kérheti a Közösség segítségét a [Microsoft Q&a Azure Active Directory kérdéseit tartalmazó oldalt](/answers/topics/azure-active-directory.html). A Közösség tagjai közé tartoznak a mérnökök, a termékmenedzsment, a MVP és az informatikai szakemberek.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Ha nem találja a választ a problémára, a támogatási csapatunk mindig szívesen segít Önnek.

A megfelelő segítség érdekében kérjük, hogy az esetek megnyitásakor a lehető legrészletesebben adja meg az adatokat. Ezek az adatok a következőket tartalmazzák:

* **A hiba általános leírása**: mi a hiba? Mi volt az észlelt viselkedés? Hogyan lehet reprodukálni a hibát? A lehető legrészletesebben adja meg az adatokat.
* **Page**: milyen oldalon voltál a hiba? Adja meg az URL-címet, ha tudja, és képernyőképet is tartalmaz az oldalról.
* **Támogatási kód**: mi volt a támogatási kód, amely akkor jött létre, amikor a felhasználó meglátta a hibát?
   * A kód megkereséséhez reprodukálja a hibát, majd válassza a képernyő alján található **támogatási kód** hivatkozást, majd küldje el az eredményeket tartalmazó GUID-t a támogatási szakembernek.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="A támogatási kód a webböngésző ablakának jobb alsó sarkában található.":::

  * Ha egy oldalon nem található támogatási kód, válassza az F12 lehetőséget, és keresse meg a SID és a CID kifejezést, és küldje el a két eredményt a támogatási szakembernek.
* **Dátum, idő és időzóna**: adja meg a pontos dátumot és időt a hiba előfordulási *időzónájában* .
* **Felhasználói azonosító**: ki volt a felhasználó, aki látta a hibát? Ilyen például a *felhasználói \@ contoso.com*.
   * Ez egy összevont felhasználó?
   * Ez egy áteresztő hitelesítési felhasználó?
   * Ez egy jelszó-kivonattal szinkronizált felhasználó?
   * Ez egy csak felhőalapú felhasználó?
* **Licencelés**: van-e hozzárendelve a felhasználó Azure ad-licenccel?
* **Alkalmazás-eseménynapló**: ha jelszó-visszaírási használ, és a hiba a helyszíni infrastruktúrában található, az alkalmazás eseménynaplójának tömörített másolatát adja meg az Azure ad Connect-kiszolgálóról.

## <a name="next-steps"></a>Következő lépések

További információ a SSPR-ről [: Hogyan működik az Azure ad önkiszolgáló jelszó-visszaállítás](concept-sspr-howitworks.md) , vagy [Hogyan működik az önkiszolgáló jelszó-visszaállítási VISSZAÍRÁSI az Azure ad-ben?](concept-sspr-writeback.md).
