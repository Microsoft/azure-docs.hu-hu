---
title: Az önkiszolgáló jelszó-visszaállítási szolgáltatás – Azure Active Directory
description: Hogyan működik az önkiszolgáló jelszóát állítás?
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94077a1c6329aa1fecf9593f2df41fa77afc8a44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765916"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Működés: Azure AD önkiszolgáló jelszóátállítás

Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználóknak, hogy rendszergazda vagy ügyfélszolgálati beavatkozás nélkül módosítsák vagy visszaállítsák a jelszavukat. Ha egy felhasználó fiókja zárolva van, vagy elfelejti a jelszavát, az utasításokat használva feloldhatja a blokkolást, és vissza is mehet a munkához. Ez a képesség csökkenti az ügyfélszolgálati hívásokat és a hatékonyság elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba.

> [!IMPORTANT]
> Ez a fogalmi cikk ismerteti egy rendszergazdának az önkiszolgáló jelszóát állítás működését. Ha Ön már regisztrált végfelhasználó az új jelszó önkiszolgáló nevére, és vissza kell szereznie a fiókját, akkor a(nak) gombra kell [https://aka.ms/sspr](https://aka.ms/sspr) állítania.
>
> Ha az it-csapat nem engedélyezte a saját jelszó alaphelyzetbe állításának képességét, további segítségért az segélycsapat segítségét kell kérnie.

## <a name="how-does-the-password-reset-process-work"></a>Hogyan működik a jelszó-visszaállítási folyamat?

A felhasználók az SSPR portálon állíthatják alaphelyzetbe vagy módosíthatják [jelszavukat.](https://aka.ms/sspr) Először regisztrálniuk kell a kívánt hitelesítési módszereket. Amikor egy felhasználó hozzáfér az SSPR portálhoz, az Azure platform a következő tényezőket veszi figyelembe:

* Hogyan kell honosizálni az oldalt?
* Érvényes a felhasználói fiók?
* Milyen szervezethez tartozik a felhasználó?
* Hol van a felhasználó jelszava?
* A felhasználó rendelkezik licenccel a funkció használatához?

Amikor egy felhasználó  a Nem lehet hozzáférni a fiókjához hivatkozást választja egy alkalmazásból vagy oldalról, vagy közvetlenül a webhelyre lép, az SSPR portálon használt nyelv a következő [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) lehetőségeken alapul:

* Alapértelmezés szerint a böngésző területi beállítása az SSPR megfelelő nyelven való megjelenítésére használható. A jelszó-visszaállítási szolgáltatás ugyanazon nyelvekre van honosított, mint a Microsoft 365 [támogatja.](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec)
* Ha egy adott honosított nyelven szeretne hivatkozni az SSPR-re, fűzheti hozzá a következőt a jelszó-visszaállítási URL-cím végéhez a szükséges `?mkt=` területi beállítások mellett: .
    * A spanyol *es-us* területi beállítás megadásához például használja a következőt: `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Miután az SSPR portál megjelenik a szükséges nyelven, a rendszer felkéri a felhasználót, hogy adjon meg egy felhasználói azonosítót, és adjon át egy captcha-et. Az Azure AD a következő ellenőrzésekkel ellenőrzi, hogy a felhasználó használhatja-e az SSPR-t:

* Ellenőrzi, hogy a felhasználónál engedélyezve van-e az SSPR, és van-e hozzárendelve Azure AD-licenc.
  * Ha a felhasználó nincs engedélyezve az SSPR-hez, vagy nincs hozzárendelve licenc, a rendszer megkéri a felhasználót, hogy lépjen kapcsolatba a rendszergazdával a jelszó visszaállításához.
* Ellenőrzi, hogy a felhasználó rendelkezik-e a megfelelő hitelesítési módszerekkel a fiókjában a rendszergazdai házirendnek megfelelően.
  * Ha a házirend csak egy módszert igényel, ellenőrizze, hogy a felhasználó rendelkezik-e a rendszergazdai házirend által engedélyezett hitelesítési módszerek legalább egyikéhez meghatározott megfelelő adatokkal.
    * Ha a hitelesítési módszerek nincsenek konfigurálva, javasoljuk a felhasználónak, hogy forduljon a rendszergazdához, és állítsa alaphelyzetbe a jelszavát.
  * Ha a házirend két módszert igényel, ellenőrizze, hogy a felhasználó rendelkezik-e a megfelelő adatokkal a rendszergazdai házirend által engedélyezett legalább két hitelesítési módszerhez.
    * Ha a hitelesítési módszerek nincsenek konfigurálva, javasoljuk a felhasználónak, hogy forduljon a rendszergazdához, és állítsa alaphelyzetbe a jelszavát.
  * Ha a felhasználóhoz Azure-rendszergazdai szerepkör van hozzárendelve, akkor az erős két kapus jelszó szabályzat lesz érvényben. További információ: A rendszergazdai [visszaállítási házirendek eltérései.](concept-sspr-policy.md#administrator-reset-policy-differences)
* Ellenőrzi, hogy a felhasználó jelszava felügyelt-e a helyszínen, például hogy az Azure AD-bérlő összevont, átmenő hitelesítést vagy jelszó-kivonatszinkronizálást használ-e:
  * Ha az SSPR-visszaírás konfigurálva van, és a felhasználó jelszava a helyszínen van kezeli, a felhasználó folytathatja a hitelesítést és a jelszó visszaállítását.
  * Ha az SSPR-visszaírás nincs telepítve, és a felhasználó jelszavát a helyszínen kezelik, a felhasználónak kapcsolatba kell lépnie a rendszergazdával a jelszó visszaállításához.

Ha az összes korábbi ellenőrzés sikeresen befejeződött, a rendszer végigvezeti a felhasználót a jelszó visszaállításának vagy módosításának folyamatán.

> [!NOTE]
> Az SSPR e-mailes értesítéseket küldhet a felhasználóknak a jelszó-visszaállítási folyamat részeként. Ezeket az e-maileket az SMTP-továbbítási szolgáltatással küldi el a rendszer, amely több régióban aktív-aktív módban működik.
>
> Az SMTP-továbbítási szolgáltatások megkapják és feldolgozják az e-mail törzsét, de nem tárolják. Az SSPR e-mail törzse, amely esetlegesen ügyfél által megadott adatokat tartalmazhat, nem lesz tárolva az SMTP-továbbítási szolgáltatás naplóiban. A naplók csak protokoll-metaadatokat tartalmaznak.

Az SSPR-sel való első lépésekhez töltse ki a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az önkiszolgáló jelszóát állítás (SSPR) engedélyezése](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>A felhasználók regisztrálásának megkövetele a bejelentkező felhasználóktól

Engedélyezheti azt a beállítást, amely megköveteli a felhasználóktól az SSPR-regisztráció befejezését, ha modern hitelesítést vagy webböngészőt használnak bármely alkalmazásba való bejelentkezéshez az Azure AD használatával. Ez a munkafolyamat a következő alkalmazásokat tartalmazza:

* Microsoft 365
* Azure Portal
* Hozzáférési panel
* Összevont alkalmazások
* Egyéni alkalmazások az Azure AD-t használva

Ha nincs szüksége regisztrációra, a rendszer nem kéri a felhasználókat a bejelentkezés során, de manuálisan is regisztrálhat. A felhasználók a regisztrációhoz vagy a Regisztráció új jelszó útjára hivatkozásra kattintva, az új jelszó regisztrálása lap Profil [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) lapján hozzáférési panel.  

![Az SSPR regisztrációs lehetőségei a Azure Portal][Registration]

> [!NOTE]
> A felhasználók bezárhatja az SSPR regisztrációs portált a **Mégse** lehetőség választásával vagy az ablak bezárásával. A rendszer azonban minden jelentkezzen be, amíg be nem fejeződik a regisztráció.
>
> Az SSPR-regisztráció megszakítása nem szakítja meg a felhasználó kapcsolatát, ha már be van jelentkezve.

## <a name="reconfirm-authentication-information"></a>Hitelesítési adatok újbóli hitelesítése

Ha meg kell győződni arról, hogy a hitelesítési módszerek helyesek, amikor szükség van rájuk a jelszavuk alaphelyzetbe állításához vagy a jelszavuk változtatásához, megkövetelheti a felhasználóktól, hogy bizonyos idő után erősítsek meg az adataik regisztrált adatait. Ez a beállítás csak akkor érhető el, ha engedélyezi a Felhasználók regisztrálásának **megkövetele a bejelentkezéskor** beállítást.

Érvényes értékek, amelyek arra kérik a felhasználót, hogy erősítse meg, a regisztrált metódusok *0 és* *730 nap között vannak.* Ha ezt az értéket *0-ra* juk, a rendszer soha nem kéri a felhasználókat a hitelesítési adataik megerősítésére. A kombinált regisztrációs folyamat használata esetén a felhasználóknak meg kell erősíteniük a személyazonosságukat, mielőtt újra megerősíteik az adataikat.

## <a name="authentication-methods"></a>Hitelesítési módszerek

Ha egy felhasználó számára engedélyezve van az SSPR, legalább egy hitelesítési módszert regisztrálnia kell. Javasoljuk, hogy két vagy több hitelesítési módszert válasszon, hogy a felhasználók rugalmasabban férnek hozzá, ha nem tudnak hozzáférni egy módszerhez, amikor szükségük van rá. További információ: [Mik azok a hitelesítési módszerek?](concept-authentication-methods.md).

Az SSPR-hez a következő hitelesítési módszerek érhetők el:

* Mobilalkalmazás-értesítés
* Mobilalkalmazás-kód
* E-mail
* Mobiltelefon
* Munkahelyi telefon
* Biztonsági kérdések

A felhasználók csak akkor állíthatják vissza jelszavukat, ha a rendszergazda által engedélyezett hitelesítési módszert regisztráltak.

> [!WARNING]
> Az Azure-rendszergazdai *szerepkörökhöz* rendelt fiókoknak a Rendszergazdai visszaállítási szabályzat eltérései című szakaszban meghatározott módszereket [kell használniuk.](concept-sspr-policy.md#administrator-reset-policy-differences)

![Hitelesítési módszerek kiválasztása a Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>A szükséges hitelesítési módszerek száma

Beállíthatja, hogy a felhasználó hány hitelesítési módszert adjon meg a jelszó visszaállításához vagy feloldásához. Ez az érték egy vagy két *értékre* *is* beállítható.

A felhasználók több hitelesítési módszert is regisztrálnak és kell is. Ismét erősen ajánlott a felhasználók számára két vagy több hitelesítési módszer regisztrálása, hogy nagyobb rugalmasságot tudjanak biztosítani arra az esetre, ha nem férnek hozzá egy módszerhez, amikor szükségük van rá.

Ha a felhasználó nem regisztrálta a minimálisan szükséges metódusokat az SSPR használata során, egy hibalap jelenik meg, amely arra kéri, hogy kérjen egy rendszergazdát a jelszó visszaállításához. Legyen gondja, ha a szükséges módszerek számát 1-ről 2-re növeli, ha vannak olyan meglévő felhasználók, akik regisztrálva vannak az SSPR-re, és ezután nem tudják használni a funkciót. További információkért tekintse meg a hitelesítési módszerek módosítása című [szakaszt.](#change-authentication-methods)

#### <a name="mobile-app-and-sspr"></a>Mobilalkalmazás és SSPR

Ha mobilalkalmazást használ a jelszó-visszaállításhoz, például a Microsoft Authenticator, a következő szempontokat kell figyelembe vennie:

* Ha a rendszergazdáknak egy módszerre van szükségük a jelszó visszaállításához, az ellenőrző kód az egyetlen elérhető lehetőség.
* Ha a rendszergazdák két módszert követelnek meg a jelszó visszaállításához, a felhasználók a többi engedélyezett módszer mellett értesítési **vagy** ellenőrző kódot is használhatnak.

| Az alaphelyzetbe állításhoz szükséges módszerek száma | Eggyel | Kettővel |
| :---: | :---: | :---: |
| Elérhető mobilalkalmazás-funkciók | Code | Kód vagy értesítés |

A felhasználóknak nincs lehetőség arra, hogy regisztrálják a mobilalkalmazásukat, amikor önkiszolgáló jelszó-visszaállításra regisztrálnak a [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) rendszerről. A felhasználók a következő e-mailben regisztrálják mobilalkalmazásukat: , vagy a kombinált biztonsági információk [https://aka.ms/mfasetup](https://aka.ms/mfasetup) regisztrációja a következő: [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Az Authenticator alkalmazás nem választható ki egyetlen hitelesítési módszerként, ha csak egy módszerre van szükség. Hasonlóképpen, az Authenticator alkalmazás és csak egy további módszer nem választható ki, ha két módszerre van szükség.
>
> Ha olyan SSPR-szabályzatokat konfigurál, amelyek metódusként tartalmazzák az Authenticator alkalmazást, legalább egy további módszert kell kiválasztani, ha az egyik módszerre szükség van, és két módszer konfigurálásakor legalább két további módszert kell kiválasztani.
>
> Ennek a követelménynek az az oka, hogy a jelenlegi SSPR-regisztrációs folyamat nem tartalmazza a hitelesítő alkalmazás regisztrálásának beállítását. A hitelesítő alkalmazás regisztrálásának lehetősége az új kombinált regisztrációs [folyamat része.](./concept-registration-mfa-sspr-combined.md)
>
> A csak az Authenticator alkalmazást használó szabályzatok (ha az egyik módszer szükséges) vagy az Authenticator alkalmazás és csak egy további módszer (ha két módszerre van szükség) engedélyezésével a rendszer letilthatja a felhasználók SSPR-regisztrációját, amíg be nem állítva, hogy az új kombinált regisztrációs élményt használják.

### <a name="change-authentication-methods"></a>Hitelesítési módszerek módosítása

Mi történik, ha olyan szabályzatot használ, amely csak egy szükséges hitelesítési módszerrel rendelkezik az alaphelyzetbe állításhoz vagy a zárolás feloldásához, és ezt két módszerre módosítja?

| Regisztrált módszerek száma | A szükséges módszerek száma | Eredmény |
| :---: | :---: | :---: |
| 1 vagy több | 1 | **Alaphelyzetbe állíthatja** vagy feloldhatja a zárolást |
| 1 | 2 | **Nem lehet** alaphelyzetbe állítani vagy feloldani a zárolást |
| 2 vagy több | 2 | **Alaphelyzetbe állíthatja** vagy feloldhatja a zárolást |

Az elérhető hitelesítési módszerek módosítása a felhasználók számára is problémákat okozhat. Ha módosítja a felhasználók által használható hitelesítési módszerek típusait, akkor véletlenül is akadályozhatja, hogy a felhasználók használni tudják az SSPR-t, ha nem állnak rendelkezésre a lehető legkevesebb adat.

Vegyük példaként a következő forgatókönyvet:

1. Az eredeti házirend két hitelesítési módszerrel van konfigurálva. Csak az irodai telefonszámot és a biztonsági kérdéseket használja.
1. A rendszergazda úgy módosítja a szabályzatot, hogy a továbbiakban ne használja a biztonsági kérdéseket, de engedélyezi a mobiltelefon és egy másodlagos e-mail használatát.
1. A mobiltelefonszám vagy a másodlagos e-mail-mezők nélkül a felhasználók most már nem állíthatják vissza jelszavukat.

## <a name="notifications"></a>Értesítések

A jelszóesemények jobb tájékoztatása érdekében az SSPR lehetővé teszi, hogy értesítéseket konfiguráljon a felhasználók és az identitás-rendszergazdák számára.

### <a name="notify-users-on-password-resets"></a>Értesítse a felhasználókat új jelszó kérésekor?

Ha a beállítás beállítása **Igen,** a jelszavukat visszaállító felhasználók e-mailt kapnak, amely értesíti őket, hogy a jelszavuk megváltozott. Az e-mailt az SSPR-portálon keresztül küldi el az Azure AD-ben tárolt elsődleges és másodlagos e-mail-címére. Senki más nem kap értesítést a visszaállítási eseményről.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Minden rendszergazda értesítése, ha más rendszergazdák új jelszót adnak vissza

Ha ez a beállítás Igen, **akkor** az összes többi Azure-rendszergazda e-mailt kap az Azure AD-ben tárolt elsődleges e-mail-címére. Az e-mail arról értesíti őket, hogy egy másik rendszergazda módosította a jelszavát az SSPR használatával.

Vegyük példaként a következő forgatókönyvet:

* Egy környezetben négy rendszergazda van.
* Az *A rendszergazda* az SSPR használatával állítja alaphelyzetbe a jelszavát.
* A *B,* *C,* *és D* rendszergazdák e-mailben figyelmeztetik őket a jelszó-visszaállításról.

## <a name="on-premises-integration"></a>Helyszíni integráció

Hibrid környezet esetén konfigurálhatja a Azure AD Connect, hogy az Azure AD-ból visszaírja a jelszóváltozási eseményeket egy helyszíni címtárba.

![A jelszóvisszaírás engedélyezése és használata][Writeback]

Az Azure AD ellenőrzi az aktuális hibrid kapcsolatot, és a következő üzenetek egyikét biztosítja a Azure Portal:

* A helyszíni visszaírási ügyfél működik.
* Az Azure AD online állapotban van, és csatlakozik a helyszíni visszaírási ügyfélhez. Úgy tűnik azonban, hogy a Azure AD Connect verziója elavult. Fontolja [meg Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) frissítését, hogy a legújabb kapcsolati funkciókkal és fontos hibajavításokkal rendelkezik.
* Sajnos nem tudjuk ellenőrizni a helyszíni visszaírási ügyfél állapotát, mert a Azure AD Connect telepített verziója elavult. [Frissítse Azure AD Connect,](../hybrid/how-to-upgrade-previous-version.md) hogy ellenőrizhető legyen a kapcsolat állapota.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaírási ügyfélhez. [A Azure AD Connect](./troubleshoot-sspr-writeback.md) helyreállításához szükséges hibák elhárítása.
* Sajnos nem tudunk csatlakozni a helyszíni visszaírási ügyfélhez, mert a jelszóvisszaírás nincs megfelelően konfigurálva. [Konfigurálja a jelszóvisszaírást](./tutorial-enable-sspr-writeback.md) a kapcsolat visszaállításához.
* Sajnos úgy tűnik, hogy jelenleg nem tudunk csatlakozni a helyszíni visszaírási ügyfélhez. Ezt ideiglenes problémák okozhatják a végén. Ha a probléma továbbra is fennáll, [Azure AD Connect](./troubleshoot-sspr-writeback.md) a kapcsolat visszaállításához.

Az SSPR-visszaírás első lépésekhez töltse ki a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Az önkiszolgáló jelszó-visszaállítás (SSPR) visszaírásának engedélyezése](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Jelszavak visszaírása a helyszíni címtárba

A jelszóvisszaírást a következő Azure Portal. Emellett ideiglenesen letilthatja a jelszóvisszaírást anélkül, hogy újra kellene konfigurálnia Azure AD Connect.

* Ha a beállítás beállítása **Igen,** akkor a visszaírás engedélyezve van. Az összevont, átmenő hitelesítéssel vagy jelszó kivonatával szinkronizált felhasználók vissza tudják állítani a jelszavukat.
* Ha a beállítás beállítása **Nem,** akkor a visszaírás le van tiltva. Az összevont, átmenő hitelesítés vagy jelszó kivonatával szinkronizált felhasználók nem tudják visszaállítani a jelszavukat.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Fiókok zárolásának feloldása a jelszó alaphelyzetbe állítása nélkül

Alapértelmezés szerint az Azure AD feloldja a fiókok zárolását, amikor új jelszót kér. A rugalmasság érdekében engedélyezheti a felhasználóknak a helyszíni fiókok zárolásának feloldását anélkül, hogy új jelszót kell megadniuk. Ezzel a beállítással válassza el egymástól ezt a két műveletet.

* Ha az **Igen lehetőséget** választja, a felhasználóknak lehetősége van a jelszó visszaállítására és a fiók zárolásának feloldására, vagy a fiók zárolásának feloldására anélkül, hogy új jelszót kell megadniuk.
* Ha a **Nem,** a felhasználók csak az új jelszó végrehajtásához és a fiók zárolásának feloldásához szükséges műveleteket hajtják végre.

### <a name="on-premises-active-directory-password-filters"></a>Helyszíni jelszószűrők Active Directory használata

Az SSPR a rendszergazda által kezdeményezett jelszó-visszaállításnak felel meg a Active Directory. Ha külső jelszószűrőt használ az egyéni jelszószabályok kényszerítése érdekében, és megköveteli, hogy ez a jelszószűrő az Azure AD önkiszolgáló jelszó-visszaállítás során legyen bejelölve, győződjön meg arról, hogy a külső féltől származó jelszószűrő megoldás konfigurálva van a rendszergazdai jelszó-visszaállítási forgatókönyvben való alkalmazásra. [Az Azure AD jelszóvédelem Active Directory Domain Services](concept-password-ban-bad-on-premises.md) alapértelmezés szerint támogatott.

## <a name="password-reset-for-b2b-users"></a>Jelszó visszaállítása B2B-felhasználók számára

A jelszó-visszaállítás és -módosítás teljes mértékben támogatott az összes vállalatok között (B2B) konfigurációban. A B2B felhasználói jelszó-visszaállítás a következő három esetben támogatott:

* **Meglévő Azure AD-bérlővel** rendelkező partnerszervezet felhasználói: Ha a partnerszervezet rendelkezik meglévő Azure AD-bérlővel, akkor minden olyan jelszó-visszaállítási szabályzatot tiszteletben tartunk, amely engedélyezve van a bérlőn. Ahhoz, hogy a jelszó-visszaállítás működjön, a partnerszervezetnek csak meg kell győződni arról, hogy az Azure AD SSPR engedélyezve van. Az ügyfelek számára nem számítunk fel Microsoft 365 díjat.
* **Önkiszolgáló** regisztrációval regisztráló felhasználók: Ha az Ön partnerszervezete az önkiszolgáló regisztráció funkciót használta a bérlőhöz való bejelentkezéshez, akkor a regisztrált [e-mail-címmel](../enterprise-users/directory-self-service-signup.md) új jelszót állíthatnak vissza.
* **B2B-felhasználók:** Az [új Azure AD B2B-képességekkel](../external-identities/what-is-b2b.md) létrehozott új B2B-felhasználók a meghívási folyamat során regisztrált e-mail-címmel is alaphelyzetbe állíthatják jelszavukat.

A forgatókönyv teszteléséhez a következő https://passwordreset.microsoftonline.com partnerfelhasználóval kell továbbmenni: . Ha másodlagos e-mail vagy hitelesítési e-mail van megadva, a jelszó-visszaállítás a várt módon működik.

> [!NOTE]
> Azok a Microsoft-fiókok, amelyek vendég hozzáférést kaptak az Azure AD-bérlőhöz, például az Hotmail.com- vagy Outlook.com-címről vagy más személyes e-mail-címről származók, nem tudják használni az Azure AD SSPR-t. Új jelszót kell megadniuk a Ha nem tud bejelentkezni a bejelentkezési adataiba a Microsoft-fiók [alapján.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

## <a name="next-steps"></a>Következő lépések

Az SSPR első lépésekhez töltse ki a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: Új jelszó önkiszolgáló beállításának (SSPR) engedélyezése](tutorial-enable-sspr.md)

Az alábbi cikkekben az Azure AD jelszóátállításáról olvashat további információkat:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Az elérhető Azure AD-hitelesítési módszerek és a szükséges mennyiség"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "SSPR-regisztrációs beállítások konfigurálása a Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Helyszíni SSPR-integráció a Azure Portal"
