---
title: Önkiszolgáló jelszó-visszaállítási jelentések – Azure Active Directory
description: Jelentéskészítés az Azure AD önkiszolgáló jelszó-visszaállítási eseményeiről
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad1b8318e261c7dfef7fc125716736087a84bdc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579178"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Az Azure AD-jelszókezelés jelentéskészítési lehetőségei

Az üzembe helyezést követően számos szervezet szeretné tudni, hogy az önkiszolgáló jelszó-visszaállítás (SSPR) valóban használatban van-e. A Azure Active Directory (Azure AD) által biztosított jelentéskészítési funkció segítséget nyújt az előre elkészített jelentések használatával kapcsolatos kérdések megválaszolásához. Ha megfelelő licenccel rendelkezik, egyéni lekérdezéseket is létrehozhat.

![Jelentéskészítés a SSPR az Azure AD-ban található naplók használatával][Reporting]

A következő kérdéseket választhatja ki a [Azure Portalban](https://portal.azure.com/)található jelentések:

> [!NOTE]
> [Globális rendszergazdának](../roles/permissions-reference.md)kell lennie, és az adatoknak a szervezet nevében való összegyűjtéséhez be kell jelentkeznie. A bekapcsolásához legalább egyszer fel kell keresnie a **jelentéskészítés** lapot vagy a naplókat. Addig nem történik meg az adatok gyűjtése a szervezet számára.
>

* Hány felhasználó regisztrált a jelszó-visszaállításra?
* Ki regisztrált a jelszó alaphelyzetbe állítására?
* Milyen adatregisztrációt használnak az emberek?
* Hány ember állítja vissza a jelszavukat az elmúlt hét napban?
* Melyek a felhasználók vagy a rendszergazdák által a jelszavuk alaphelyzetbe állításához használt leggyakoribb módszerek?
* Mik azok a gyakori problémák a felhasználók vagy a rendszergazdák számára, amikor a jelszó-visszaállítást próbálják használni?
* Milyen rendszergazdák gyakran alaphelyzetbe állítja a saját jelszavukat?
* Van gyanús tevékenység a jelszó-visszaállítással?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>A jelszókezelési jelentések megtekintése az Azure Portalon

A Azure Portal-élményben továbbfejlesztettük a jelszavak alaphelyzetbe állítása és a jelszó-visszaállítás regisztrációs tevékenység megtekintésének módját. Az alábbi lépések segítségével megkeresheti a jelszó-visszaállítási és a jelszó-visszaállítási regisztrációs eseményeket:

1. Tallózással keresse meg a [Azure Portal](https://portal.azure.com).
2. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget.
3. Keresse meg **Azure Active Directory** a szolgáltatások listájában, és válassza ki.
4. Válassza a **felhasználók** lehetőséget a kezelés szakaszban.
5. Válassza a **naplók** lehetőséget a **felhasználók** panelen. Ez megjeleníti az összes olyan naplózási eseményt, amely a címtár összes felhasználóján bekövetkezett. A nézet szűrésével megtekintheti az összes jelszóval kapcsolatos eseményt.
6. A panel tetején található **szűrő** menüben válassza ki a **szolgáltatás** legördülő listát, és módosítsa az **önkiszolgáló jelszó-kezelő** szolgáltatás típusára.
7. Ha szeretné, a lista további szűréséhez válassza ki az Önt érdeklő **tevékenységet** .

### <a name="combined-registration"></a>Kombinált regisztráció

Ha engedélyezte a [kombinált regisztrációt](./concept-registration-mfa-sspr-combined.md), a naplók felhasználói tevékenységével kapcsolatos információk a **biztonsági**  >  **hitelesítési módszerek** alatt találhatók.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>A jelentés oszlopainak leírása a Azure Portalban

A következő lista részletesen ismerteti a Azure Portal jelentés oszlopait:

* **Felhasználó**: az a felhasználó, aki megkísérelte a jelszó-visszaállítási regisztrációs műveletet.
* **Szerepkör**: a felhasználó szerepe a címtárban.
* **Dátum és idő**: a kísérlet dátuma és időpontja.
* **Regisztrált információk**: a felhasználó által a jelszó-visszaállítási regisztráció során megadott hitelesítő információk.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>A jelentés értékeinek leírása a Azure Portalban

A következő táblázat a Azure Portal egyes oszlopaihoz beállítható különböző értékeket ismerteti:

| Oszlop | Megengedett értékek és jelentésük |
| --- | --- |
| Regisztrált adatkezelés |**Másodlagos e-mail**: a felhasználó egy másodlagos e-mail-vagy hitelesítési e-mailt használt a hitelesítéshez.<p><p>**Irodai telefon**: a felhasználó egy Office-telefont használt a hitelesítéshez.<p>**Mobiltelefon**: a felhasználó mobiltelefont vagy hitelesítési telefont használt a hitelesítéshez.<p>**Biztonsági kérdések**: a felhasználó biztonsági kérdéseket használt a hitelesítéshez.<p>**Az előző módszerek, például a másodlagos e-mailek és a mobiltelefon bármely kombinációja**: akkor következik be, amikor kétkapus házirend van megadva, és a felhasználó a jelszó-visszaállítási kérelem hitelesítéséhez használt két módszert jeleníti meg. |

## <a name="self-service-password-management-activity-types"></a>Self-Service jelszavas kezelés tevékenységtípusok

A következő tevékenységtípusok jelennek meg az **önkiszolgáló jelszavas kezelés** naplózási eseményének kategóriájában:

* [Az önkiszolgáló jelszó-visszaállítás blokkolva](#activity-type-blocked-from-self-service-password-reset): azt jelzi, hogy egy felhasználó megpróbált alaphelyzetbe állítani egy jelszót, egy adott kaput vagy egy telefonszámot, amely 24 órán belül legfeljebb öt alkalommal érvényesíthető.
* [Jelszó módosítása (önkiszolgáló)](#activity-type-change-password-self-service): azt jelzi, hogy a felhasználó önkéntes vagy kényszerített (lejárat miatti) jelszó-módosítást hajtott végre.
* [Jelszó alaphelyzetbe állítása (a rendszergazda által)](#activity-type-reset-password-by-admin): azt jelzi, hogy a rendszergazda a Azure Portal egy felhasználó nevében jelszó-visszaállítást hajtott végre.
* [Jelszó alaphelyzetbe állítása (önkiszolgáló)](#activity-type-reset-password-self-service): azt jelzi, hogy a felhasználó sikeresen visszaállította a jelszavát az [Azure ad jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com).
* Önkiszolgáló [jelszó-visszaállítási folyamat tevékenységének folyamata](#activity-type-self-serve-password-reset-flow-activity-progress): jelzi, hogy a felhasználó milyen lépésekkel halad át, például egy adott jelszó-visszaállítási hitelesítési kapu átadásával a jelszó-visszaállítási folyamat részeként.
* [Felhasználói fiók zárolásának feloldása (önkiszolgáló)](#activity-type-unlock-a-user-account-self-service): azt jelzi, hogy a felhasználó az [Azure ad jelszó-visszaállítási Active Directory portál](https://passwordreset.microsoftonline.com) jelszavának visszaállítása nélkül sikeresen feloldotta a Active Directory fiókját, és nem kell alaphelyzetbe állítani a fiók zárolását.
* Az [önkiszolgáló jelszó-visszaállításhoz regisztrált felhasználó](#activity-type-user-registered-for-self-service-password-reset): azt jelzi, hogy a felhasználó regisztrálta az összes szükséges információt, hogy a jelszó a jelenleg megadott bérlői jelszó-visszaállítási szabályzatnak megfelelően legyen visszaállítva.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tevékenységtípus: letiltva az önkiszolgáló jelszó-visszaállítástól

A következő lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: azt jelzi, hogy egy felhasználó megpróbált alaphelyzetbe állítani egy jelszót, egy adott kaput, vagy egy telefonszámot ötnél több, mint öt teljes időtartam alatt 24 órán belül.
* **Tevékenység színésze**: az a felhasználó, aki a további visszaállítási műveletek végrehajtásával lett szabályozva. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység célja**: a további visszaállítási műveletek végrehajtásával szabályozott felhasználó. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység állapota**:
  * _Sikeres_: azt jelzi, hogy egy felhasználó a további visszaállítások végrehajtásával, a további hitelesítési módszerek megadásával vagy a következő 24 órában További telefonszámok érvényesítésével lett szabályozva.
* **Tevékenység állapotának meghibásodási oka**: nem alkalmazható.

### <a name="activity-type-change-password-self-service"></a>Tevékenység típusa: jelszó módosítása (önkiszolgáló)

A következő lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: azt jelzi, hogy a felhasználó önkéntes vagy kényszerített (lejárati) jelszó megváltozása miatt végzett.
* **Tevékenység színésze**: a jelszót megváltoztató felhasználó. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység célja**: az a felhasználó, aki megváltoztatta a jelszavát. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységek állapota**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen módosította a jelszavát.
  * _Hiba_: azt jelzi, hogy a felhasználó nem tudta módosítani a jelszavát. A sor kiválasztásával megtekintheti a **tevékenység állapotának okának** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt meg a hiba.
* **Tevékenység állapotának meghibásodási oka**:
  * _FuzzyPolicyViolationInvalidPassword_: a felhasználó olyan jelszót adott meg, amelyet a rendszer automatikusan letiltott, mert a Microsoft által tiltott jelszó-észlelési képességek túl gyakoriak vagy különösen gyengék.

### <a name="activity-type-reset-password-by-admin"></a>Tevékenység típusa: jelszó alaphelyzetbe állítása (a rendszergazda által)

A következő lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: azt jelzi, hogy a rendszergazda a Azure Portal egy felhasználó nevében jelszó-visszaállítást hajtott végre.
* **Tevékenység színésze**: az a rendszergazda, aki egy másik végfelhasználó vagy rendszergazda nevében új jelszót hajtott végre. A jelszó-rendszergazdának, a felhasználói rendszergazdának vagy a segélyszolgálat rendszergazdájának kell lennie.
* **Tevékenység célja**: az a felhasználó, akinek a jelszavát alaphelyzetbe állították. A felhasználó lehet végfelhasználó vagy másik rendszergazda.
* **Tevékenységek állapota**:
  * _Sikeres_: azt jelzi, hogy egy rendszergazda sikeresen visszaállította a felhasználó jelszavát.
  * _Hiba_: azt jelzi, hogy egy rendszergazda nem tudta módosítani a felhasználó jelszavát. A sor kiválasztásával megtekintheti a **tevékenység állapotának okának** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt meg a hiba.

### <a name="activity-type-reset-password-self-service"></a>Tevékenység típusa: jelszó alaphelyzetbe állítása (önkiszolgáló)

A következő lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: azt jelzi, hogy a felhasználó sikeresen visszaállította a jelszavát az [Azure ad jelszó-visszaállítási portálról](https://passwordreset.microsoftonline.com).
* **Tevékenység színésze**: a jelszó alaphelyzetbe állítására szolgáló felhasználó. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység célja**: az a felhasználó, aki alaphelyzetbe állítja a jelszavát. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységek állapota**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen visszaállította a saját jelszavát.
  * _Hiba_: azt jelzi, hogy a felhasználó nem tudta visszaállítani a saját jelszavát. A sor kiválasztásával megtekintheti a **tevékenység állapotának okának** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt meg a hiba.
* **Tevékenység állapotának meghibásodási oka**:
  * _FuzzyPolicyViolationInvalidPassword_: a rendszergazda kiválasztott egy jelszót, amelyet a rendszer automatikusan letiltott, mert a Microsoft által tiltott jelszó-észlelési képességek túl gyakoriak vagy különösen gyengék.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tevékenység típusa: önkiszolgáló jelszó-visszaállítási folyamat tevékenységének állapota

A következő lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: azt jelzi, hogy a felhasználó milyen lépésekkel halad át (például egy adott jelszó-visszaállítási hitelesítési kapu átadásával) a jelszó-visszaállítási folyamat részeként.
* **Tevékenység színésze**: a jelszó-visszaállítási folyamat részét képező felhasználó. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység célja**: az a felhasználó, aki a jelszó-visszaállítási folyamat részét végezte. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenységek állapota**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen végrehajtotta a jelszó-visszaállítási folyamat egy adott lépését.
  * _Hiba_: azt jelzi, hogy a jelszó-visszaállítási folyamat egy adott lépése nem sikerült. A sor kiválasztásával megtekintheti a **tevékenység állapotának okának** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt meg a hiba.
* A **tevékenység állapotának okai**: a következő táblázat tartalmazza az [összes engedélyezett visszaállítási tevékenység állapotának okát](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tevékenység típusa: felhasználói fiók zárolásának feloldása (önkiszolgáló)

A következő lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: azt jelzi, hogy a felhasználó sikeresen feloldotta a Active Directory fiókját anélkül, hogy új jelszót kellene beállítania az [Azure ad jelszó-visszaállítási Active Directory portálról](https://passwordreset.microsoftonline.com) , és nem kell alaphelyzetbe állítania a fiók zárolását.
* **Tevékenység színésze**: az a felhasználó, aki a jelszavuk visszaállítása nélkül zárolta a fiókját. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység célja**: az a felhasználó, aki a jelszavuk visszaállítása nélkül zárolta a fiókját. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Engedélyezett tevékenységek állapota**:
  * _Sikeres_: azt jelzi, hogy a felhasználó sikeresen feloldotta a saját fiókját.
  * _Hiba_: azt jelzi, hogy a felhasználó nem tudta feloldani a fiókját. A sor kiválasztásával megtekintheti a **tevékenység állapotának okának** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt meg a hiba.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tevékenység típusa: a felhasználó az önkiszolgáló jelszó-visszaállításhoz regisztrálva van

A következő lista részletesen ismerteti ezt a tevékenységet:

* **Tevékenység leírása**: azt jelzi, hogy egy felhasználó regisztrálta az összes szükséges információt, hogy a jelszó a jelenleg megadott bérlői jelszó-visszaállítási szabályzatnak megfelelően legyen visszaállítva. 
* **Tevékenység színésze**: a jelszó-visszaállításra regisztrált felhasználó. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Tevékenység célja**: a jelszó-visszaállításra regisztrált felhasználó. A felhasználó lehet végfelhasználó vagy rendszergazda.
* **Engedélyezett tevékenységek állapota**:
  * _Sikeres_: azt jelzi, hogy egy felhasználó sikeresen regisztrált a jelszó-visszaállításra az aktuális szabályzatnak megfelelően. 
  * _Hiba_: azt jelzi, hogy a felhasználó nem tudott regisztrálni a jelszó-visszaállításhoz. A sor kiválasztásával megtekintheti a **tevékenység állapotának okának** kategóriáját, ha többet szeretne megtudni arról, hogy miért történt meg a hiba.

     >[!NOTE]
     >A hiba nem jelenti azt, hogy a felhasználó nem tudja visszaállítani a saját jelszavát. Ez azt jelenti, hogy nem fejezték be a regisztrációs folyamatot. Ha a fiókjában nem ellenőrzött adatok szerepelnek, például a telefonszámot, amely nincs érvényesítve, még akkor is, ha nem ellenőrizte ezt a telefonszámot, továbbra is használhatja a jelszavát.

## <a name="next-steps"></a>Következő lépések

* [SSPR és MFA-használati és-bejelentési jelentések](./howto-authentication-methods-activity.md)
* [Hogyan végezhető el az SSPR sikeres bevezetése?](howto-sspr-deployment.md)
* [Új jelszó kérése vagy jelszó módosítása](../user-help/active-directory-passwords-update-your-own-password.md).
* [Regisztráció új jelszó önkiszolgáló kérésére](../user-help/active-directory-passwords-reset-register.md).
* [Van licencelési kérdése?](concept-sspr-licensing.md)
* [Milyen adatokat használ az SSPR, és milyen adatokat kell kitöltenie a felhasználók számára?](howto-sspr-authenticationdata.md)
* [Milyen hitelesítési módszerek érhetők el a felhasználók számára?](concept-sspr-howitworks.md#authentication-methods)
* [Mik az SSPR szabályzatbeállításai?](concept-sspr-policy.md)
* [Mi a jelszóvisszaíró, és miért fontos?](./tutorial-enable-sspr-writeback.md)
* [Mik az SSPR beállításai, és mit jelentenek?](concept-sspr-howitworks.md)
* [Úgy gondolom, hogy valami megszakadt. Hogyan a SSPR hibáinak megoldása?](./troubleshoot-sspr.md)
* [Olyan kérdésem van, amely máshol nem szerepelt](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Példa SSPR tevékenység-naplózási naplókra az Azure AD-ben"